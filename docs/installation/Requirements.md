# Requirements

Zabbix has specific hardware and software requirements that must be met,
and these requirements may change over time. They also depend on the size of your
setup and the software stack you select.
Before purchasing hardware or installing a database version, it's essential to
consult the Zabbix documentation for the most up-to-date requirements for the
version you plan to install.
You can find the latest requirements [here](https://www.zabbix.com/documentation/7.0/en/manual/installation/requirements).
Make sure to select the correct Zabbix version from the list.

For smaller or test setups, Zabbix can comfortably run on a system with 2 CPUs
and 8 GB of RAM. However, your setup size, the number of items you monitor,
the triggers you create, and how long you plan to retain data will impact
resource requirements.
In today’s virtualized environments, my advice is to start small and scale up
as needed.

You can install all components (Zabbix server, database, web server) on a single
machine or distribute them across multiple servers.
For simplicity, take note of the server details:

| Component       | IP Address |
| :-------------- | :--------- |
| Zabbix Server   |            |
| Database Server |            |
| Web Server      |            |

???+ tip
    Zabbix package names often use dashes (`-`) in their names, such as `zabbix-get`
or `zabbix-sender`, but the binaries themselves may use underscores (`_`),
like `zabbix_sender` or `zabbix_server`. This naming discrepancy can sometimes
be confusing, particularly if you are using packages from non-official Zabbix
repositories.
Always check if a binary uses a dash or an underscore when troubleshooting.

---

## Basic OS Configuration

### Firewall

Before installing Zabbix, it's essential to properly prepare the operating system.
The first step is to ensure that the firewall is installed and configured.

To install and enable the firewall, run the following command:

```bash
# dnf install firewalld --now
```

Once installed, you can configure the necessary ports.
For Zabbix, we need to allow access to port `10051/tcp`, which is where the
Zabbix trapper listens for incoming data. Use the following command to open
this port in the firewall:

```bash
# firewall-cmd --add-service=Zabbix-server --permanent
```

If the service is not recognized, you can manually specify the port:

```bash
# firewall-cmd --add-port=10051/tcp --permanent
```

/// note | firewalld
"Firewalld is the replacement for iptables in RHEL-based systems and allows
changes to take effect immediately without needing to restart the service.
If your distribution does not use [Firewalld](https://www.firewalld.org),
refer to your OS documentation for the appropriate firewall configuration steps."
///

---

### Time Server

Another crucial step is configuring the time server and syncing the Zabbix server
using an NTP client.
Accurate time synchronization is vital for Zabbix, both for the server and the
devices it monitors.
If one of the hosts has an incorrect time zone, it could lead to confusion, such
as investigating an issue in Zabbix that appears to have happened hours earlier
than it actually did.

To install Chrony, an NTP client, use the following command:

```bash
# dnf install chronyd --now
```

Once installed, you can verify that Chrony is enabled and running by checking
its status:

```bash
# systemctl status chronyd
```

/// note | dnf
"dnf is a package manager used in Red Hat-based systems. If you're using another
distribution, replace `dnf` with your appropriate package manager, such as `zyper`,
`apt`, or `yum`. Chrony is a modern replacement for `ntpd`, offering faster and
more accurate time synchronization.
If your OS does not support [Chrony](https://chrony-project.org/), consider using
`ntpd` instead."
///

Once Chrony is installed, the next step is to ensure the correct time zone is set. You can view your current time configuration using the `timedatectl` command:

```bash
# timedatectl
               Local time: Thu 2023-11-16 15:09:14 UTC
           Universal time: Thu 2023-11-16 15:09:14 UTC
                 RTC time: Thu 2023-11-16 15:09:15
                Time zone: UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

Ensure that the Chrony service is active (refer to the previous steps if needed).
To set the correct time zone, first, you can list all available time zones with
the following command:

```bash
# timedatectl list-timezones
```

This will display a list of time zones, from which you can select the one closest
to your location, for example:

```bash
Africa/Abidjan
Africa/Accra
...
Pacific/Tongatapu
Pacific/Wake
Pacific/Wallis
UTC
```

Once you've identified your time zone, configure it using the following command:

```bash
# timedatectl set-timezone Europe/Brussels
```

To verify that the time zone has been configured correctly, use the `timedatectl`
command again:

```bash
# timedatectl
               Local time: Thu 2023-11-16 16:13:35 CET
           Universal time: Thu 2023-11-16 15:13:35 UTC
                 RTC time: Thu 2023-11-16 15:13:36
                Time zone: Europe/Brussels (CET, +0100)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

???+ note
    Some administrators prefer installing all servers in the UTC time zone to
    ensure that server logs across global deployments are synchronized.
    Zabbix supports user-based time zone settings, which allows the server to
    remain in UTC while individual users can adjust the time zone via the
    interface if needed.

---

### Verifying Chrony Synchronization

To ensure that Chrony is synchronizing with the correct time servers, you can
run the following command:

```bash
# chronyc
```

The output should resemble:

```bash
chrony version 4.2
Copyright (C) 1997-2003, 2007, 2009-2021 Richard P. Curnow and others
chrony comes with ABSOLUTELY NO WARRANTY.  This is free software, and
you are welcome to redistribute it under certain conditions.  See the
GNU General Public License version 2 for details.

chronyc>
```

Once inside the Chrony prompt, type the following to check the sources:

```bash
chronyc> sources
```

Example output:

```bash
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^- 51-15-20-83.rev.poneytel>     2   9   377   354   +429us[ +429us] +/-  342ms
^- 5.255.99.180                  2  10   377   620  +7424us[+7424us] +/-   37ms
^- hachi.paina.net               2  10   377   412   +445us[ +445us] +/-   39ms
^* leontp1.office.panq.nl        1  10   377   904  +6806ns[ +171us] +/- 2336us
```

In this example, the NTP servers in use are located outside your local region.
It is recommended to switch to time servers in your country or, if available,
to a dedicated company time server. You can find local NTP servers
[here](https://www.ntppool.org/).

---

### Updating Time Servers

To update the time servers, modify the `/etc/chrony.conf` file. Replace the existing
NTP server with one closer to your location.

Example of the current configuration:

```bash
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
pool 2.centos.pool.ntp.org iburst
```

Change it to a local time server:

```bash
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
pool be.pool.ntp.org iburst
```

After making this change, restart the Chrony service to apply the new configuration:

```bash
# systemctl restart chronyd
```

### Verifying Updated Time Servers

Check the time sources again to ensure that the new local servers are in use:

```bash
chronyc> sources
```

Example of expected output with local servers:

```bash
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^- ntp1.unix-solutions.be        2   6    17    43   -375us[ -676us] +/-   28ms
^* ntp.devrandom.be              2   6    17    43   -579us[ -880us] +/- 2877us
^+ time.cloudflare.com           3   6    17    43   +328us[  +27us] +/- 2620us
^+ time.cloudflare.com           3   6    17    43
```

This confirms that the system is now using local time servers.
