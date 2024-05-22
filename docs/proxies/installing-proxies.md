# Installing Proxies

There are some cases where monitoring with the Zabbix server and agents …. is not enough. 
Imagine monitoring external clients, different VLANs or just monitoring a DMZ where you don’t want to open for every agent a port in the firewall for the agent, SNMP, IPMI, … 
Here Zabbix proxies come to the rescue.
A Zabbix proxy is a small remote Zabbix server that supports the collection of data. With Zabbix 7 it’s now even possible to run Zabbix proxies in HA.
Proxies will also become important in larger environments to simplify the management and most importantly to offload the Zabbix server. Our proxies can do pre-processing and it will also limit the number of connections from devices to the Zabbix server.

So in short a Zabbix proxy can be used to :
- Monitor remote locations
- Monitor locations that have unreliable connections
- Offload the Zabbix server when monitoring thousands of devices
- Simplify the maintenance and management

## Proxy requirements

If you like to setup a few proxies for test or in your environment you will need a few Linux hosts to install the Proxies on. 
Proxies are also available in containers so a full VM is not needed. However here we will use a VM so we can show you how to install a proxy.
When it comes to proxies they are very lightweight however since Zabbix 4.2 Proxies are able to do Item value preprocessing and this can use a lot of CPU power.
So the number of CPUs and memory will depends on how many machines you will monitor and how many preprocessing rules you have on your hosts.


???+ note
    Imagine that you need to restart your Zabbix server and that all proxies start to push the data they have gathered during the downtime of the Zabbix server. This would create ahuge amount of data being sent at once to the Zabbix server and bring it to its knees in no time.
    Since Zabbix 6 Zabbix has added protection for overload. When Zabbix server history cache is full the history cache write access is being throttled. Zabbix server will stop accepting data from proxies when history cache usage reaches 80%. Instead those proxies will be put on a throttling list.
    This will continue until the cache usage falls down to 60%. Now server will start accepting data from proxies one by one, defined by the throttling list. This means the first proxy that attempted to upload data during the throttling period will be served first and until it's done the server will not accept data from other proxies.

|History write cache usage 	| Zabbix server mode	| Zabbix server action 	|
|----				|----			|----			|
|Reaches 80%			|Wait			|Stops accepting proxy data, but maintains a throttling list (prioritized list of proxies to be contacted later).|
|Drops to 60%			|Throttled		|Starts processing throttling list, but still not accepting proxy data.	|
|Drops to 20%			|Normal			|Drops the throttling list and starts accepting proxy data normally.|


## Configuration

Zabbix actually has 2 proxy modes it supports. A Zabbix proxy can be Active or Passive just like the Zabbix agent with this exception that the agent can be both on the same time.
So our first problem will be choose the Zabbix proxy that fits us. When we talk about active / passive we always talk about the proxy being active or passive.
Active means the Proxy will initiate the connection to the Zabbix server, passive means it will be the Zabbix server taking contact with the proxy to gather the data.

### Active vs Passive proxy

Zabbix proxies have been available since Zabbix 1.6, at that time they where available only as what we know today as ```Active proxies```. Active means that the proxy would initiate the connection by itself to the Zabbix Server.
Since version 1.8.3 passive proxies where introduced. This allowed the server to connect to the proxy. As mentioned before Zabbix agents can be both active and passive however proxies cannot be both so whe have to choose the way of the communication when we install a proxy.
Just remember that choosing the proxy mode ```active``` or ```passive``` has no impact on how Zabbix agents can communicate with our proxy. It's perfectly fine to have an ```active proxy``` and a ```passive agent``` working together.


### Active proxy


Let's first start with the setup of an active Proxy.
Things should be very simple just make sure you have a VM that you can use to install a proxy.

#### Zabbix Gui config

First we will have to add the config in our Zabbix server frontend. From our Administration menu choose the menu ```Proxies```.

![menu-proxies](image/menu.png)

Go to the upper right corner of the screen and press ```Create proxy```
You will now get a pop-up like shown here. Fill in the proxy name. I have chosen for ProxyA as it will be our active proxy. You can use for yourself whatever you like. In production I would suggest to use names that make it easy for you to see it's a proxy and where the proxy belongs to. Ex: what vlan or what division ....

Choose Proxy mode ```Active``` and fill in the IP of your virtualmachine.

![proxya](image/proxya-config.png)

When done press ```Add``` at the bottom.


#### Zabbix Proxy config

Ok now that this is done lets go to the VM where we like to install our proxy and add the Zabbix repository so we can install our needed packages.

```
rpm -Uvh https://repo.zabbix.com/zabbix/7.0/rhel/9/x86_64/zabbix-release-7.0-1.el9.noarch.rpm
```

Zabbix provides the proxy with the option to connecto to a SQLiteDB this can be enough for most setups. In case you need more performance from the DB there is also the option to install the Proxy with a PostgreSQL or MySQL DB. We will cover how to do with the SQLite db as this is the most easy way. We show you later what to look for if installing a proxy With MySQL or PostgreSQL.


```
dnf install zabbix-proxy-sqlite3 zabbix-selinux-policy -y
```

Once this is done let's change the proxy configuration by editing the proxy configuration file.

```
vi /etc/zabbix/zabbix_proxy.conf
```
Here a few parameters needs to be changed before we can use our proxy

- Server=
- DBName=
- Hostname=

Change them according to your settings:

- Server=< zabbix server ip >
- Hostname=ProxyA ( or the name you have used in your Zabbix frontend for the Proxy )
- DBName=/tmp/zabbix_proxy.db

The parameters are explained as follows:

- Server: Same as with active agents this parameter is used to connect to the Zabbix server, we can use the IP or DNS name here.
- DBName: This depends a bit, it is usually the name of the database like we configured on the Zabbix server in case we use PostgreSQL or MySQL. However with SQLite we need to specify dbname and location.
          Username and Password will be ignored when using SQLite. Just make sure SELinux is properly configured else the DB will not be created. It can help to put SELinux in permissive first.
- Hostname: This parameter is used by the proxy to identify itself to the Zabbix server. This needs to be the same name we have registered in the Zabbix frontend.

???+ tip 
    Another parameter that can be useful is ListenPort this is usually 10051 for Active and Passive proxies. But if you run for some reason the proxy on the zabbix server then you have to change this port to something else.

???+ note
    With Zabbix 7 a new parameter has been introduced on the proxy side ```ProxyBufferMode```. This is standard set to ```Hybrid``` and is what is recommended. With Hybrid the proxy will buffer will work in memory and fallback to the database if needed.
    The documentation states that the proxy buffer normally works like in memory mode until it runs out of memory or the oldest record exceeds the configured age. If that happens the buffer is flushed
    to database and it works like in disk mode until all data have been uploaded and it starts working with memory again. On shutdown the memory buffer is flushed to database.

???+ note
    Don't worry about the file for the db not existing Zabbix will create it by itself.

???+ warning
    If you use DNS names make sure there is DNS caching configured on the machine. If not Zabbix will do a lookup on the DNS server everytime it wants to make a connection.
  

So now that we have configured everything let's start our proxy. First disable SELinux as it will block a few things this can be done by running ```setenforce 0```. This is only temporary and either you keep it disabled permanent or you fix the config.
An easy way is to use ```sealert -a /var/log/audit/audit.log```.
Once this is done there is only 1 thing that rests us to do and that is start and enable the zabbix-proxy service.

```
systemctl enable zabbix-proxy --now
```

If you look now in the frontend of the Zabbix server you will see that the proxy we have configured is Online

![proxy active](image/ProxyA-active.png)


???+ note
    If you like to install the proxy with MySQL or PostgreSQL as database then it is important to also install the package ```zabbix-sql-scripts```. There is a special shema to be used for the proxy database that can be found under ```/usr/share/zabbix-sql-scripts/mysql|postgresql>/proxy.sql```.
    Also in this case don't forget to edit the zabbix_proxy.conf file and add DBHost, DBUser, DBPassword and DBSchema as needed.


### Passive proxy




