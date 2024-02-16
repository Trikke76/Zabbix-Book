# Zabbix hosts

To understand how Zabbix works it's important to know that ```Host``` in Zabbix are a reference to anything we would like to monitor it can be a physical host, a virtual machine, an application a device or even just a dummy host used to calculate data from existing other hosts into something new.

Its probably one of the first tasks that we will do as an Admin when we first login to Zabbix because we need a host if we would like to monitor some metrics. It's however important to know that hosts cannot be created without them being in a hostgroup. 

With this said let's create our first host.

Let's go to the menu on our ```Left``` and select Data Collection -> Hosts.
We see that there is already a hosts configured and that the availability icon is "RED" don't worry about it this is normal we have no Zabbix agent installed or configured.

To Add a new host to our system we press ```Create host`` this button can be found in the upper right corner of our screen.

![Create a host](image/zabbix-add-hosts.png)

We now get a popup where we need to fill in some information about our host.

![Create a host](image/zabbix-new-host.png)

<html>
<head>
<style>
thead th { text-align:left; background:grey; color:white}
tbody th { text-align:right; background: lightgrey; color:whitwhitee}
</style>
</head>
<body>
<table>
<thead>
<tr>
<th>Parameter</th><th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<th>Host name</th><td>Here we need to enter the <b> Host name </b> of our machine we like to add. The name can contain alphanumerics, spaces, dots, dashes and underscores. HOWEVER you are not allowed to use leading and trailing spaces. The <b>Host name</b> in the frontend is what we need later for the configuration of our Zabbix agent so make sure you remember it. </td>
</tr>
<tr>
<th>Visible name</th><td>The <b>host name</b> as we have seen is neede to configure our <b>Zabbix agent</b>. So in case you like to give it a unique name that is random generated etc you can add a visible name here. This name will then be used in the frontend instead of what we call the technical name <b>host name</b>. This name has support for UTF-8 so special characters are supported.This name will be used in all the places like maps, latest data, inventory , ...</td>
</tr>
<tr>
<th>Templates</th><td>Templates are like blueprints that we can use on our hosts to add items, triggers , .... We explain more about it in the topic <b>Zabbix templates</b>. You can start typing the name of the template and Zabbix will start to show a list with matches or you can press the ```Select``` box and choose one from the list.  </td>
</tr>
<tr>
<th>Host groups</th><td>Every host needs to be in atleast 1 ```host group```. This is because permssions are set on host groups. You can type the name of the <b>host group</b> and a list of matching groups will start to appear. Another way is to select a host group from an existing list by pressing the <b>Select</b>button. Or you can create a new group by just typing the name and pressing on the box that shows the name of the group you typed with (new) behind it</td>
</tr>
<tr>
<th>Interfaces</th><td>Zabbix supports several host interfaces like Zabbix agent, SNMP, JMX and IPMI. By default when we create a host no interface is added. To add an interface press <b>Add</b> and fill in the needed info like IP or DNS depending on the host interface chosen. When an interface is in use (items created that use the interface) then the interface cannot be removed.</td>
</tr>
<tr>
<th>Description</th><td>A place to enter a short description about our host.</td>
</tr>
<tr>
<th>Monitored by proxy</th><td>If we have proxies configured we can select here if we like to monitor our host by a proxy.</td>
</tr>
<tr>
<th>Enabled</th><td>Mark the checkbox to enable the host. This will make it monitored by Zabbix. When unchecked the host will not be monitored.</td>
</tr>
</tbody>
</table>
</body>
</html>
