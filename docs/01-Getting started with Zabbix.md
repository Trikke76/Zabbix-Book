## Intro


## Preparing the OS for Zabbix

It's important for our zabbix server to have an OS that is well prepared before we start to install our monitoring tool. First we need to make sure our firewall is installed

``dnf install firewalld --now``

Our firewall is installed now and we are ready to configure the needed ports. For our zabbix server we need to allow access to port 10051/tcp this is the port where our zabbix trapper listens on for incoming data.

```firewall-cmd --add-service=zabbix-server --permanent```
or 
```firewall-cmd --add-port=10051/tcp --permanent```

Another thing we need to configure is the setup of timeserver and sync our zabbix server to the timeserver by making use of an ntp client. This needs to be done for the Zabbix server but also for the devices we will monitor as time is very important for Zabbix. Imagine one of our hosts having a timezone that is wrong we could end up looking for a problems in Zabbix that happened 6h ago while it had happened maybe only 2h ago.

```dnf install chronyd --now```

Once Chrony is installed we also need to setup our correct timezone.
```
timedatectl
               Local time: Thu 2023-11-16 15:09:14 UTC
           Universal time: Thu 2023-11-16 15:09:14 UTC
                 RTC time: Thu 2023-11-16 15:09:15
                Time zone: UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

Make sure NTP is active q
We can choose the correct timezone from a list that we can lookup with the following command

```timedatectl list-timezones```

```
Africa/Abidjan
Africa/Accra

...

Pacific/Tongatapu
Pacific/Wake
Pacific/Wallis
UTC
```

We can now configure our correct timezone 

```
timedatectl set-timezone Europe/Brussels
```

```
# timedatectl
               Local time: Thu 2023-11-16 16:13:35 CET
           Universal time: Thu 2023-11-16 15:13:35 UTC
                 RTC time: Thu 2023-11-16 15:13:36
                Time zone: Europe/Brussels (CET, +0100)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

We can test if our Chrony is syncronizing with the correct timeservers as well by running the command chronyc

```
# chronyc
chrony version 4.2
Copyright (C) 1997-2003, 2007, 2009-2021 Richard P. Curnow and others
chrony comes with ABSOLUTELY NO WARRANTY.  This is free software, and
you are welcome to redistribute it under certain conditions.  See the
GNU General Public License version 2 for details.

chronyc>
```

Then we type sources

```
chronyc> sources
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^- 51-15-20-83.rev.poneytel>     2   9   377   354   +429us[ +429us] +/-  342ms
^- 5.255.99.180                  2  10   377   620  +7424us[+7424us] +/-   37ms
^- hachi.paina.net               2  10   377   412   +445us[ +445us] +/-   39ms
^* leontp1.office.panq.nl        1  10   377   904  +6806ns[ +171us] +/- 2336us
```

Here we see that we are using a bunch of ntp servers that are not in our own country so we better swicht to some timeservers in our local country or if we have a timeserver in our company we could use this one. We can find some local timeservers here : https://www.ntppool.org/

To change this we have to edit our config file "/etc/chrony.conf" and replace the existing ntp server with our local one 

```
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
pool 2.centos.pool.ntp.org iburst
```

```
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
pool be.pool.ntp.org iburst
```

Don't forget to restart the ntpd client of course.
```
systemctl restart chronyd
```

When we look again we will see that we are now using our local timeservers.

chronyc> sources
```
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^- ntp1.unix-solutions.be        2   6    17    43   -375us[ -676us] +/-   28ms
^* ntp.devrandom.be              2   6    17    43   -579us[ -880us] +/- 2877us
^+ time.cloudflare.com           3   6    17    43   +328us[  +27us] +/- 2620us
^+ time.cloudflare.com           3   6    17    43   +218us[  -83us] +/- 2815us
```


## Installing Zabbix with MySQL

For our installation with MySQL we first have to add a repo for MySQL


## Installing Zabbix with PostgreSQL

ToDo

## Setting up Zabbix HA

ToDo

