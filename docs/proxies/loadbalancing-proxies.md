# Proxy loadbalancing

With the introduction of Zabbix 7.0 proxy loadbalancing has been introduced. After running the server in a native HA setup this was probably the next logical step to add.
So like the name implies it's not just a simple HA solution but Zabbix proxies will loadbalance the hosts connected to them in an automated way.

Zabbix has added something new to manage how we group those proxies together with the obvious name ```Proxy groups``` and it works for active and for passive proxies.

Zabbix is able to detect 3 states in our group with proxies

 - Online: This implies that everything is working as expected so there was communication with all the proxies.
 - Offline: The minimum number of proxies defined in our proxy config group has not been met.
 - Recovering: Some proxies are offline but we still have enough proxies online to not drop below the setting ```minimum proxies```.


## Configuration

The first thing we have to do is of course set up a couple of proxies. Here I will make use of 3 active proxies but feel free to use passives if you prefer or to do a mix.
If you don't know how to install a proxy take a look at the installation guides for [Active proxies](active-proxy.md) or [Passive proxies](passive-proxy.md).

I have set up 3 proxies for this example:

- ProxyA1
- ProxyA2
- ProxyA3

each of them has a unique IP.

So once you have set up 3 proxies and have added them into the frontend like we did for active and passive proxies you should have a similar looking config in your Administration frontend under Proxies.

![3 active proxies](image/3-active-proxies.png)


Before we start to create a loadbalancing group there are a few steps we need to fix first. We need to monitor our proxies and we also need to install some Zabbix agengs on the proxies.
To install the agents we have to follow a few simple steps.

On every proxy run the following commands:

```dnf install zabbix-agent```

We already installed the zabbix repository when we installed the proxy so no need to add it again.
Now we have to adjust a few configuration options.

- Server: A list of IP adressess or DNS names pointing to our zabbix servers. This is for the Passive agent only
- ServerActive: A list of IP adressess or DNS names pointing to our zabbix servers. This is for the Active agent only.
- Hostname: The hostname of our proxy server

Do this for all the agents that are installed on the proxies and when done start the agents and enable them to start automatic.

```
systemctl enable zabbix-agent --now
```

Our next step will be to create our proxies in the Zabbix frontend to monitor them.
For this we go to ```Data collection -> Hosts``` and click on ```Create host```.

Next we have to fill in a few fields:

- Host name: Name of the agent on the proxy. I used the same name for my agent as on the proxy this makes it easy to see when a proxy is down. Ex ProxyA1, ProxyA2, ProxyA3
- Templates: We will want to monitor the OS and the proxy so we have to apply 2 templates : Linux by Zabbix agent and Zabbix proxy health
- Host groups: Here we choose a group to put our proxies is you can choose what you want this is just a logical grouping used for permissions.
- Interface: Every hosts needs to have an interface where we define the IP of the host and the port.


![proxy agent config](image/proxy-agent-config.png)

Make sure your proxies are properly monitored before you continue to the next step.

## Configure proxy groups


As one of the final steps we have to Create a ```proxy group``` to gather all proxies we want to use for our loadbalancing.
In our Zabbix menu go to ```Administration -> Proxy groups```
Click in the upper right corner on ```Create proxy group```.

Here we have to define a few options:

- Name : The name of our proxy group. This can be anything you like I have chosen for the group ```Active proxies``` because I have setup 3 proxies for this test.
- Failover period: Period in seconds before failover is executed (1m by default; allowed range 10s-15m).
- Minimum number of proxies: The minimum number of online proxies required for the group to be online (1 by default; allowed range 1-1000). Since we have 3 proxies I added 2 as minimum.

![proxy group](image/proxy-group.png)

As a final step we have to confgiure our 3 existing proxies to join this proxy group.
Go in the Zabbix menu to ```Administration -> Proxies```
click on every proxy in the list you would like to add to the group and enter following details.


- Proxy group: ```Active proxies``` (or whatever name you gave to your proxy group.)
- Proxy address: The active proxy ip, If specified then active proxy requests are only accepted from this list of comma-delimited IP addresses, optionally in CIDR notation, or DNS names of active Zabbix proxy.
