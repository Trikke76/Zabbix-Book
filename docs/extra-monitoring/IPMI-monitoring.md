# IPMI Monitoring

We have already seen a few ways to monitor with Zabbix what we havent seen yet is monitoring with IPMI. 
IPMI stands for ```Intelligent Platform Management Interface``` and is usually an extra interface in your hardware that needs to be added.
It's a set of standard specification for hardware platforms that allows to control and monitor the hardware.

In this chapter we will see how to :
- Configuring an IPMI device
- Configure Zabbix to monitor an IPMI device
- Setting up an IPMI item
- Configure LLD with IPMI
- Monitor discrete sensors


## Configuring an IPMI device

Usually you need some hardware device with an IPMI interface. This can be for example a iDrac interface from DELL or an ILO interface from HP.
Every hardware vendor has some card based on IPMI but names can be vendor specific

If you don't have a hardware device that supports open IPMI don't worry you can install an ipmi simulator. It does not provide any network information but provides the basic functionality to play with.

To Install the container run the following commands:

```
sudo dnf install podman
sudo podman pull vaporio/ipmi-simulator
sudo podman run --name ipmi_simulator --publish 623:623/udp docker.io/vaporio/ipmi-simulator:latest
sudo dnf install ipmitool
```

Once done everything should be up and running and we can test to see if we can connect to it .

ipmitool -H 127.0.0.1 -U ADMIN -I lanplus -P ADMIN chassis status

if you like to do it from another VM that is also possible we will have to do it later from our Zabbix server anyway but then don't forget to open the firewall port 623/tcp.

```
firewall-cmd --add-port=623/tcp --permanent
firewall-cmmd --reload
---

???+ Note
    It's good to know that IPMI is not free from security problems. Intel , Dell, HP , ... even encourage not to use it and we should not expect any updates on the IPMI specifications in the future. (See the documentation)[https://www.intel.com/content/www/us/en/products/docs/servers/ipmi/ipmi-home.html?wapkw=ipmi]

## Configure Zabbix to monitor an IPMI device

Before we can start configuring items in Zabbix we need to configure the Zabbix server to start some IPMI pollers. This can be done in the ```zabbix_server.conf``` file.
Zabbix relies on the OpenIPMI library to monitor IPMI. So to get things started let's edit ```/etc/zabbix/zabbix_server.conf``` and change the line ```# StartIPMIPollers=0```.


## Setting up an IPMI item
## Configure LLD with IPMI
## Monitor discrete sensors
