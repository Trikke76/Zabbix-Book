# IPMI Monitoring

We have already seen a few ways to monitor with Zabbix what we havent seen yet is monitoring with IPMI. 
IPMI stands for ```Intelligent Platform Management Interface``` and is usually an extra interface in your hardware that needs to be added.
It's a set of standard specification for hardware platforms that allows to control and monitor the hardware.

An IPMI system has several key components:

- BMC (Baseboard management controller) : This is a microcontroller that monitors and manages the hardware of the system independently even when the server is offline.
- IPMB (Intelligent platform management bus) : This is a framework that allows interactions between all the IPMI components. This is the most important part for us as it has a key role in system monitoring by supporting sensor data collection and the execution of control commands.
- IPMI Memory : All sensor data, configuration settings and event logs will be stored in memory to make it easier to run diagnostics during failures when the server is offline.
- Dedicated management network : This network is designed explicitly for IPMI traffic, providing a reliable channel of communication with the BMC.

The features that are provided by IPMI are:

- Out-of-band management:  A feature that enables the remote management of servers independently of the operating system and it's status.
It is neededfor performing firmware updates, troubleshooting, and executing recovery procedures without the need for direct physical access to the hardware.
- Hardware monitoring : IPMI provides real-time information  of critical system parameters, like temperature, voltage, fan speed, power consumption, disk health, .... .
By monitoring these metrics, IPMI can help to quicklyidentify and address potential issues before they escalate.
- Event logging: The System Event Log (SEL), records system events, this will offering valuable resources for post-event analysis and troubleshooting.
- Remote power control: This offers administrators control over the equipment's power state from a distance. It maes it easy to turn devices on/off, performing resets, and cycling power for system management and recovery.
- Virtual media: IPMI enables mounting remote storage devices as though they were physically connected to the server, this makes remote installations more easy.
- Security features: IPMI includes security to authenticate user identity and control access permissions. This ensures only authorized users can access.


???+ note
    When writing, the landscape of IPMI is defined by two prominent versions: 1.5 and 2.0. Although both versions are in active use, IPMI 2.0 is distinguished by its advanced features, heightened security measures, and superior performance, making it the preferred option for modern server management.


In this chapter you will learn how to :

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
```
ipmitool -H 127.0.0.1 -U ADMIN -I lanplus -P ADMIN chassis status
```

if you like to do it from another VM that is also possible we will have to do it later from our Zabbix server anyway but then don't forget to open the firewall port 623/udp.

```
firewall-cmd --add-port=623/udp --permanent
firewall-cmmd --reload
```

???+ Note
    It's good to know that IPMI is not free from security problems. Intel , Dell, HP , ... even encourage not to use it and we should not expect any updates on the IPMI specifications in the future. https://www.intel.com/content/www/us/en/products/docs/servers/ipmi/ipmi-home.html?wapkw=ipmi

## Configure Zabbix to monitor an IPMI device

Before we can start configuring items in Zabbix we need to configure the Zabbix server to start some IPMI pollers. This can be done in the ```zabbix_server.conf``` file.
Zabbix relies on the OpenIPMI library to monitor IPMI. So to get things started let's edit ```/etc/zabbix/zabbix_server.conf``` and change the line ```# StartIPMIPollers=0```.


## Setting up an IPMI items

Before we can dive into Zabbix and start adding items we need to be sure we have access to our IPMI. IPMI makes use of port 623 and works on top of UDP, so it's in the OSI 5-7 range so we need to be sure there is no firewall blocking this port.
Make sure you have enough pollers in the ```zabbix_server.conf```  for example ```StartIPMIPollers=3``` and dont forget to restart the server.


We can test the access to the IPMI console with the following command:
```
ipmitool -U <User> -H <IP address of the IPMI host> -I lanplus -P <password> -L user sensor
```

The -L is to give user access so that we dont need any administrative privileges. The ```lanplus``` option instructs our ipmitool to use the IPMI 2.0 Lan interface and the sensor queries the host for a list of available sensors. You could launch the command without the word sensor then the command will return you a list of options to choose from.

+??? info
    We have used the openipmi tool so far to query the IPMI interface Zabbix however uses the OpenIPMI library for this. So a working ipmitool is not guarantee that Zabbix will work or vice versa. Also if you use the container for testing then the last command will not work as the test container is rather limited in what it can do. For more options check out the webpage of the owner https://github.com/vapor-ware/ipmi-simulator.

Let us launch the following command to see the Power status of our machine:

```
# ipmitool -H 192.168.0.23 -U ADMIN -I lanplus -P ADMIN chassis power status
Unable to Get Channel Cipher Suites
Chassis Power is 
```

#







## Configure LLD with IPMI
## Monitor discrete sensors
