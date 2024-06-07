# SNMP trap monitoring

If you have been reading previous topic about SMNP monitoring then you know by now that SNMP monitoring is all about polling information form a device.
However there is another way to gather information from a device with SNMP that does not expect us to poll for information but that accepts information being sent from the SNMP device to our Zabbix server or Proxy.
This is what we call SNMP traps.

Traps are sent from a device to our Zabbix server or proxy and usually are only being sent when there is a change in the status of a device. Traps also dont use TCP but are being sent over UDP to port 162. So why would we use traps as we already can poll SNMP devices ?

Well first of all let's imagine there is a short fluke in the power of your switch for only a second. This would never be noticed by Zabbx as we usually poll an item once per minute or even less. If we had to poll every second it would create a huge load on the device and we would maybe overload it and we would need also a more beefy Zabbix server if we had to to this for every item.

A trap is sent when a problem is detected so this is extra data that else would probably be missed by Zabbix. There are of course some disadvantages with traps else we would only use traps and never poll for problems. The problem with traps is that they are sent over UDP and the problem with UDP is that packages are being sent without waiting for any acknowledgement that they have arrived at the destination. It's a bit like talking to your wife not even knowing she is in the same room and then thinking she has understood you.

We all know how that works out ....

So the best way to monitor SNMP devices is probably an implementation of both SNMP polling and SNMP traps.

There is one small problem that is not really a problem but complicates things a bit and that is that Zabbix has no build in trap receiver. Zabbix can use SNMP traps but it needs to rely on the OS for this and so we need to install snmptrapd to catch traps when they are bein sent to our Zabbix server/proxy .

Once we have catched the trap we need to process the trap to make it readable by Zabbix and for this there are 2 ways:

- We can make use of a script usually a perl script that is delivered with the sourcecode from Zabbix or a bash script
- by making use of SNMPTT.

Since there are 2 solutions it makes sense that they both have advantages and disadvantages as you probably already know.

- The perl/bash script is the most easy solution and quit performant but more difficult to make customizations so less flexible.
- The SNMPTT,snmptrapfmt, ...  is more complex but give more flexiblity.

This table will give us a short overview of the differences between the 2 solutions:

|			| Perl Script		| SNMPTT		|
| ----			| ----			| ----			|
| MIBS			| Not Required		| Required		|
| Trap formatting	| Script		| Config file		|
| Trap matching		| snmptrap["PCRE"]	| snmptrap["PCRE"]	|
| Unknown traps		| snmptrap.fallback	| Configuration file	|
| Acc or reject trap	| No			| Yes			|
| Search and replace	| No			| Yes			|

The perl script is the most easy way so if you don't need any of the fancy features that are not possible with the perl script it's probably best to stick to this script. In this book we will cover the script. The other way is more or less the same but you will have to do some tuning yourself on the config of SNMPTT or other solution.

The script can be found in the source code. You can download the source [here](https://www.zabbix.com/download_sources). Once downloaded you can find the perl script in the folder ```/misc/snmptrap/zabbix_trap_receiver.pl``` If you can not use perl you can find the bash script in the same location.

## Setting up snmptraps 

Now that we know a bit more about snmptraps let's set it up and configure our Zabbix server. Traps are sent to the zabbix server so in case we have a proxy insteqd of the zabbix server we need to send traps to the proxy.

???+ warning
    At this moment Zabbix has loadbalancing for proxies implemented but there is no support for SNMP traps in this case. So only the active proxy will accept traps.

First thing first we will have to open our firewall if we want to accept traps. Traps are being sent over UDP and arrive on port 162 so let's open a port.

```
# firewall-cmd --add-port=162/udp --permanent
# firewall-cmd --reload
```

Next we need to install the net-snmp-utils package and the net-snmp-perl package for our perl script to work.

```
dnf install -y net-snmp-utils net-snmp-perl
```

Copy our zabbix_trap_receiver file to /usr/bin/ and make it executable

```
# cp zabbix_trap_receiver.pl /usr/bin/.
# chmod +x /usr/bin/zabbix_trap_receiver.pl
```

We can now configure ```snmptrapd```  and tell our server what traps it should accept.

```
# vi /etc/snmp/snmptrapd.conf
add the following line
# authCommunity execute public perl do "/usr/bin/zabbix_trap_receiver.pl";
```

This line will tell our ```snmptrapd``` to accept all the traps that are being sent to our device with the community string set as public. so please adapt it to your needs.

We also need to tell our Zabbix server to start up the trapper process this can be done in the configuration file of the server. Edit the following file 

```
# vi /etc/zabbix/zabbix_server.conf
```
And look for the line with the parameter ```StartSNMPTrapper=``` and make sure that the line is active by removing the # in front and replace `0` with `1` so that Zabbix knows it needs to activate the trapper for SNMP

```
StartSNMPTrapper=1
```

There is another parameter that we need to configure and that is ```SNMPTrapperFile=```. Same here make sure that the line is active by removing the `#` in front and poit to the location of the location where Zabbix can read the traps that are written by our perl script.
If it's not the same then adapt the script or next line so that both files point to the same location: The perl script will write with trap information in this file and Zabbix server will look for this file to read the information.

```
SNMPTrapperFile=/var/log/zabbix_traps.tmp
```
Once everything is done restart the Zabbix server so that it picks up the config changes.

```
# systemct restart zabbix-server
```
Also enable ```snmptrapd``` and make sure it starts at boot.

```
# systemctl enable snmptrapd --nowl
```

There is one thing we need to do extra and that is to configure log rotate. Snmptrapd will sent traps to the ```/var/log/zabbix_traps.tmp``` file and the file weill keep growing and growing so we need to make sure logrotate will cleanup from time to time.

First create a folder to archive everything

```
# mkdir -p /var/log/zabbix_traps_archive
and add the correct permissions
# chmod 770 /var/log/zabbix_traps_archive
```

Now create the logrotate config for our trap file

```
# vi /etc/logrotate.d/zabbix_traps
```

Copy following content in to this file and adqpt to your own needs.

```
/var/logs/zabbix_traps.tmp { 
   weekly 
   size 10M 
   compress 
   notifempty 
   dateext 
   dateformat -%Y%m%d 
   olddir /var/log/zabbix_traps_archive 
   maxage 365 
   rotate 10 
} 
```

## Create a trap item in Zabbix

## Testing the traps

```
- #snmptrap -v 1 -c public 127.0.0.1 '.1.3.6.1.6.3.1.1.5.4' '0.0.0.0' 6 33 '55' .1.3.6.1.6.3.1.1.5.4 s "eth0"`
- # snmptrap -v 2c -c public localhost '' 1.3.6.1.4.1.8072.2.3.0.1 1.3.6.1.4.1.8072.2.3.2.1 i 123456
```

### Check that traps are received 

After sending the traps have a look in ```/var/log/zabbix_traps.tmp```

### Working with SNMPv3 Traps

createUser -e <engineid> <user> SHA <key> AES <key>
authUser log,execute <user>
perl do "/usr/bin/zabbix_trap_receiver.pl";


???+ tip
    The PDU info can be removed from the zabbix_trap_reciever.pl in case you dont like it

Remove or add the folowing lines in comment:

```
DU INFO:
  notificationtype               TRAP
  version                        0
  receivedfrom                   UDP: [127.0.0.1]:41840->[127.0.0.1]
  errorstatus                    0
  messageid                      0
  community                      public
  transactionid                  2
  errorindex                     0
  requestid                      0
VARBINDS:
  DISMAN-EVENT-MIB::sysUpTimeInstance type=67 value=Timeticks: (55) 0:00:00.55
  SNMPv2-MIB::snmpTrapOID.0      type=6  value=OID: IF-MIB::linkUp.0.33
  IF-MIB::linkUp                 type=4  value=STRING: "eth0"
  SNMP-COMMUNITY-MIB::snmpTrapCommunity.0 type=4  value=STRING: "public"
  SNMPv2-MIB::snmpTrapEnterprise.0 type=6  value=OID: IF-MIB::linkUp
```





## Some useful links

https://www.unix.com/man-page/redhat/8/logrotate/
