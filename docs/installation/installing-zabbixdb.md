# Installing Zabbix DB Server

This chapter focuses on the installation of the Zabbix database. While Zabbix supports
various SQL databases, we will concentrate on three primary options:
PostgreSQL, MariaDB, and MySQL.
It's worth noting that Oracle is also supported, but its support is deprecated.
Zabbix 7 will be the final version supporting Oracle as a backend.

We will outline the installation and configuration process for each database on
a separate machine.
However, it's important to understand that there's no strict requirement to
install the database on a separate server from your Zabbix server.
The choice between local and remote database installation depends on your specific
needs and infrastructure.

For most initial setups, a locally installed database will likely suffice.
When opting for local installation, follow these best practices:

- Ensure the database is stored on separate disks from the operating system.
- Monitor performance as your system grows.
- Be prepared to migrate the database to a separate server if performance issues arise.

???+ note
    Local connections are typically faster than connections over TCP.
    Therefore, moving the database to its own server isn't always the optimal solution.
    The best configuration depends on your specific requirements and system architecture.


When deciding between local and remote database installation, consider the
following factors:

1. **System Resources:** Evaluate whether your Zabbix server has sufficient resources
to handle both the application and database workload.
2. **Network Latency:** Assess the impact of network latency on database queries
if opting for a remote installation.
3. **Scalability:** Consider future growth and the potential need for dedicated
database hardware.
4. **Backup and Maintenance:** Determine if separate database installation
simplifies your backup and maintenance procedures.

In the following sections, we'll dive into the installation and configuration
processes for PostgreSQL, MariaDB, and MySQL.
Each section will provide step-by-step instructions to ensure a smooth setup,
regardless of whether you choose local or remote installation.

---

## Installing Zabbix with MariaDB

To begin the installation process for the MariaDB server, the first step involves
manually creating a repository configuration file.
This file, `mariadb.repo`, must be placed in the `/etc/yum.repos.d/` directory.
The repository file will allow your package manager to locate and install the
necessary MariaDB components.

To create the MariaDB repository file, execute the following command in your terminal:

---

### Add the MariaDB repository

```bash
# sudo nano /etc/yum.repos.d/mariadb.repo
```

This will open a text editor where you can input the repository configuration details.
Once the repository is configured, you can proceed with the installation of
MariaDB using your package manager.

```bash
# vi /etc/yum.repos.d/mariadb.repo
```

The above command will open a new file, allowing you to define the repository
configuration. After the file is created, you'll need to add the following
content to configure the MariaDB repository.
Ensure that the version specified, in this case, `10.11`, is supported by Zabbix
by reviewing the latest version [requirements](https://www.zabbix.com/documentation/current/en/manual/installation/requirements) for your specific Zabbix deployment.

Here’s the configuration you need to add:

```ini
# MariaDB 10.11 RedHatEnterpriseLinux repository list - created 2023-11-01 14:20 UTC
# https://mariadb.org/download/
[mariadb]
name = MariaDB
# rpm.mariadb.org is a dynamic mirror if your preferred mirror goes offline. See https://mariadb.org/mirrorbits/ for details.
# baseurl = https://rpm.mariadb.org/10.11/rhel/$releasever/$basearch
baseurl = https://mirror.23m.com/mariadb/yum/10.11/rhel/$releasever/$basearch
# gpgkey = https://rpm.mariadb.org/RPM-GPG-KEY-MariaDB
gpgkey = https://mirror.23m.com/mariadb/yum/RPM-GPG-KEY-MariaDB
gpgcheck = 1
```

After saving the file, ensure that everything is properly set up and that your
MariaDB version is compatible with your Zabbix version to avoid potential
integration issues.

Before proceeding with the MariaDB installation, it’s a best practice to ensure
your operating system is up-to-date with the latest patches and security fixes.
This will help maintain system stability and compatibility with the software
you're about to install.

To update your OS, run the following command:

```bash
# dnf update -y
```

This command will automatically fetch and install the latest updates available
for your system, applying security patches, performance improvements, and bug fixes.
Once the update process is complete, you can move forward with the MariaDB installation.

### Install the MariaDB database

With the operating system updated and the MariaDB repository configured, you are
now ready to install the MariaDB server and client packages.
This will provide the necessary components to run and manage your database.

To install the MariaDB server and client, execute the following command:

```bash
# dnf install MariaDB-server MariaDB-client
```

This command will download and install both the server and client packages,
enabling you to set up, configure, and interact with your MariaDB database.
Once the installation is complete, you can proceed to start and configure
the MariaDB service.

Now that MariaDB is installed, we need to enable the service to start automatically
upon boot and start it immediately. Use the following command to accomplish this:

```bash
# systemctl enable mariadb --now
```

This command will both enable and start the MariaDB service.
Once the service is running, you can verify that the installation was successful
by checking the version of MariaDB using the following command:

```bash
# mysql -V
```

The expected output should resemble this:

```bash
mysql  Ver 15.1 Distrib 10.11.6-MariaDB, for Linux (x86_64) using EditLine wrapper
```

To ensure that the MariaDB service is running properly, you can check its status
with the following command:

```bash
# systemctl status mariadb
```

You should see an output similar to this, indicating that the MariaDB service is active and running:

```bash
● mariadb.service - MariaDB 10.11.6 database server
     Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; preset: disabled)
    Drop-In: /etc/systemd/system/mariadb.service.d
             └─migrated-from-my.cnf-settings.conf
     Active: active (running) since Sat 2023-11-18 19:19:36 CET; 2min 13s ago
       Docs: man:mariadbd(8)
             https://mariadb.com/kb/en/library/systemd/
    Process: 41986 ExecStartPre=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (code=exited, status=0/SUCCESS)
    Process: 41987 ExecStartPre=/bin/sh -c [ ! -e /usr/bin/galera_recovery ] && VAR= ||   VAR=`cd /usr/bin/..; /usr/bin/galera_recovery`; [ $? -eq 0 ]   && systemctl set-environment _WSREP_START>
    Process: 42006 ExecStartPost=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (code=exited, status=0/SUCCESS)
   Main PID: 41995 (mariadbd)
     Status: "Taking your SQL requests now..."
      Tasks: 9 (limit: 12344)
     Memory: 206.8M
        CPU: 187ms
```

This confirms that your MariaDB server is up and running, ready for further configuration.

---

### Securing the MariaDB database

To enhance the security of your MariaDB server, it's essential to remove
unnecessary test databases, anonymous users, and set a root password.
This can be done using the mariadb-secure-installation script, which provides 
a step-by-step guide to securing your database.

Run the following command:

```bash
# mariadb-secure-installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] n
 ... skipping.

You already have your root account protected, so you can safely answer 'n'.

Change the root password? [Y/n] y
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

The mariadb-secure-installation script will guide you through several key steps:

1. Set a root password if one isn't already set.
2. Remove anonymous users.
3. Disallow remote root logins.
4. Remove the test database.
5. Reload the privilege tables to ensure the changes take effect.

Once complete, your MariaDB instance will be significantly more secure.

---

### Create the Zabbix database

With MariaDB now set up and secured, we can move on to creating the database for Zabbix.
This database will store all the necessary data related to your Zabbix server,
including configuration information and monitoring data.

Follow these steps to create the Zabbix database:

-  Log in to the MariaDB shell as the root user:

You'll be prompted to enter the root password that you set during the
mariadb-secure-installation process.

```bash
# mysql -uroot -p
```

- Create the Zabbix database:

Once you're logged into the MariaDB shell, run the following command to create
a database for Zabbix:

```sql
MariaDB [(none)]> CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
```

This command creates a new database named zabbix with the UTF-8 character set,
which is required for Zabbix.

- Create a dedicated user for Zabbix and grant the necessary privileges:
Next, you need to create a user that Zabbix will use to access the database.
Replace password with a strong password of your choice.

```sql
MariaDB [(none)]> CREATE USER 'zabbix-web'@'<zabbix server ip>' IDENTIFIED BY '<password>';
MariaDB [(none)]> CREATE USER 'zabbix-srv'@'<zabbix server ip>' IDENTIFIED BY '<password>';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix-srv'@'<zabbix server ip>';
MariaDB [(none)]> GRANT SELECT, UPDATE, DELETE, INSERT ON zabbix.* TO 'zabbix-web'@'<zabbix server ip>';
MariaDB [(none)]> FLUSH PRIVILEGES;
```

This creates new users for zabbix-web and zabbix-srv, grants them  access to the
zabbix database, and ensures that the privileges are applied immediately.

- In some cases, especially when setting up Zabbix with MariaDB, you might
encounter issues related to stored functions and triggers if binary logging is
enabled.

To address this, you need to set the log_bin_trust_function_creators option to 1
in the MariaDB configuration file.
This allows non-root users to create stored functions and triggers without
requiring SUPER privileges, which are restricted when binary logging is enabled.

```sql
MariaDB [(none)]> SET GLOBAL log_bin_trust_function_creators = 1;
MariaDB [(none)]> QUIT
```

At this point, your Zabbix database is ready, and you can proceed with configuring
the Zabbix server to connect to the database.

???+ warning
    In the Zabbix documentation, it is explicitly stated that **deterministic
    triggers** need to be created during the schema import.
    On MySQL and MariaDB systems, this requires setting `GLOBAL log_bin_trust_function_creators = 1`
    if binary logging is enabled, and you lack superuser privileges.

    If the `log_bin_trust_function_creators` option is not set in the MySQL
    configuration file, it will block the creation of these triggers during
    schema import. This is essential because, without superuser access, non-root
    users cannot create triggers or stored functions unless this setting is applied.

    To summarize:

    - **Binary logging enabled:** If binary logging is enabled and the user does
    not have superuser privileges, the creation of necessary Zabbix triggers will
    fail unless `log_bin_trust_function_creators = 1` is set.
    - **Solution:** Add `log_bin_trust_function_creators = 1` to the `[mysqld]` 
    section in your MySQL/MariaDB configuration file or temporarily set it at
    runtime with `SET GLOBAL log_bin_trust_function_creators = 1` if you have
    sufficient permissions.

    This ensures that Zabbix can successfully create the required triggers during
    schema import without encountering privilege-related errors.

---

### Add the Zabbix repository and populate the DB

```bash
# rpm -Uvh https://repo.zabbix.com/zabbix/6.5/rocky/9/x86_64/zabbix-release-6.5-2.el9.noarch.rpm
# dnf clean all
# dnf install zabbix-sql-scripts
```

Upload the data from zabbix (db structure, images, user, ... )

```bash
# zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uroot -p zabbix
```

???+ warning
    Depending on the speed of your hardware or virtual machine, the process may
    take anywhere from a few seconds to several minutes. Please be patient and
    avoid canceling the operation; just wait for the prompt to appear.

Log back into your MariaDB Database as root

```bash
# mysql -uroot -p
```

Once the import of the Zabbix schema is complete and you no longer need the
`log_bin_trust_function_creators` global parameter, it is a good practice to
remove it for security reasons.

To revert the change and set the global parameter back to `0`, use the following
command in the MariaDB shell:

```sql
MariaDB [(none)]> SET GLOBAL log_bin_trust_function_creators = 0;
Query OK, 0 rows affected (0.001 sec)
```

This command will disable the setting, ensuring that the server's security posture
remains robust.

---

### Configure the firewall

Configuring Firewall Rules for MariaDB Access
To complete our database setup, we need to configure the firewall to allow incoming
connections for the MariaDB database from our Zabbix server. Currently, no
connections are accepted. Let's examine the current firewall configuration:

```bash
# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client  ssh
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

To securely allow MariaDB access, we'll create a dedicated zone and open port 3306/tcp
only for the Zabbix server's IP address:

- Create a new firewall zone for MariaDB:

```bash
# firewall-cmd --new-zone=mariadb-access --permanent
success
```

- Reload the firewall to apply changes:

```bash
# firewall-cmd --reload
success
```

- Verify the new zone has been added:

```bash
# firewall-cmd --get-zones
block dmz drop external home internal mariadb-access nm-shared public trusted work
```

- Add the Zabbix server's IP to the new zone:

```bash
# firewall-cmd --zone=mariadb-access --add-source=<zabbix-serverip> --permanent
```

- Open port 3306/tcp in the new zone:

```bash
success
# firewall-cmd --zone=mariadb-access --add-port=3306/tcp  --permanent
```

- Reload the firewall again to apply the latest changes:

```bash
success
# firewall-cmd --reload
```

To verify the new firewall rules, inspect the mariadb-access zone:

```bash
# firewall-cmd --zone=mariadb-access --list-all

mariadb-access (active)
  target: default
  icmp-block-inversion: no
  interfaces:
  sources: <ip from zabbix-server>
  services:
  ports: 3306/tcp
  protocols:
  forward: no
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```
With these configurations in place, the database server is now ready to accept
connections from the Zabbix server. You can proceed with the next
task: [Installing the Zabbix Server](installing-zabbix.md)

---

## Installing Zabbix with MySQL

Before proceeding with the MySQL server installation, it's crucial to set up the
appropriate MySQL repository. This ensures that we install the correct version
compatible with our Zabbix implementation.

- Verify Supported MySQL Versions Prior to installation, consult the official Zabbix
[documentation](https://www.zabbix.com/documentation/current/en/manual/installation/requirements)
to confirm the supported MySQL versions. This step is critical for several reasons:

- **Compatibility**: Ensures that the MySQL version works seamlessly with your
Zabbix installation.
- **Support**: Avoids installing versions that are no longer supported or maintained.
- **Performance**: Guarantees optimal performance and utilization of features
specific to Zabbix requirements.

- Create MySQL Repository
To install the proper MySQL server files, you need to add the official MySQL
repository to your system. This process typically involves the following steps:
a. Download the MySQL repository configuration file.
b. Add the repository to your system's package manager.
c. Update the package manager's cache to recognize the new repository.
The exact commands for these steps may vary depending on your operating system
and the specific MySQL version required for your Zabbix installation.

- Prepare for Installation
Once the repository is set up and you've confirmed the appropriate MySQL version,
you'll be ready to proceed with the actual installation of the MySQL server.

By following these preliminary steps, you ensure a smooth installation process and
avoid potential compatibility issues down the line. The next section will cover
the actual installation procedure for the MySQL server.

[documentation](https://www.zabbix.com/documentation/current/en/manual/installation/requirements)
to see what version is supported so you don't install a version that is not
supported or is not supported anymore.

---

### Add the MySQL repo

Run the following command to install the MySQL repo for version 8.0

`# dnf -y install https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm`

???+ note
    "If you install this on RedHat 8 and higher or alternatives like CentOS, Rocky or Alma 8 then you need to disable the OS provided MySQL module by running 'module disable mysql'."

Let's update our OS first with the latest patches

```bash
# dnf update -y
```

This command will automatically fetch and install the latest updates available
for your system, applying security patches, performance improvements, and bug fixes.
Once the update process is complete, you can move forward with the MariaDB installation.

---

### Installing the MySQL database

With the operating system updated and the MySQL repository configured, you are
now ready to install the MySQL server and client packages. 
This will provide the necessary components to run and manage your database.

To install the MySQL server, execute the following command:

`# dnf -y install mysql-community-server`

Now that MySQL is installed, we need to enable the service to start automatically
upon boot and start it immediately. Use the following command to accomplish this:

```bash
# systemctl enable mysqld --now
```

Once the installation is complete, you can verify the version of the MySQL server by using the following command:

`# mysql -V`

The output should look like this:

`mysql  Ver 8.0.35 for Linux on x86_64 (MySQL Community Server - GPL)`

To ensure that the MySQL service is running properly, you can check its status
with the following command:

```bash
# systemctl status mysqld

● mysqld.service - MySQL Server
     Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; preset: disabled)
     Active: active (running) since Mon 2023-11-20 22:15:51 CET; 1min 15s ago
       Docs: man:mysqld(8)
             http://dev.mysql.com/doc/refman/en/using-systemd.html
    Process: 44947 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
   Main PID: 45012 (mysqld)
     Status: "Server is operational"
      Tasks: 37 (limit: 12344)
     Memory: 448.3M
        CPU: 4.073s
     CGroup: /system.slice/mysqld.service
             └─45012 /usr/sbin/mysqld

Nov 20 22:15:43 mysql-db systemd[1]: Starting MySQL Server...
Nov 20 22:15:51 mysql-db systemd[1]: Started MySQL Server.
```

---

### Securing the MySQL database

MySQL will secure our database with a random root password that is generated when
we install the database. First thing we need to do is replace it with our own password.
To find what the password is we need to read the log file with the following command:

```bash
# grep 'temporary password' /var/log/mysqld.log
```

Change the root password as soon as possible by logging in with the generated,
temporary password and set a custom password for the superuser account:

```bash
# mysql -uroot -p
```

```sql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '<my mysql password>';
mysql> quit
```
To enhance the security of your MySQL server, it's essential to remove unnecessary
test databases, anonymous users, and set a root password.
This can be done using the mysql-secure-installation script, which provides a
step-by-step guide to securing your database.

???+ note
    "There is no need to reset the root password for MySQL again as we have reset
    it already. The next step is optional but recommended."

```bash
# mysql_secure_installation

Securing the MySQL server deployment.

Enter password for user root:
The 'validate_password' component is installed on the server.
The subsequent steps will run with the existing configuration
of the component.
Using existing password for root.

Estimated strength of the password: 100
Change the password for root ? ((Press y|Y for Yes, any other key for No) : n

 ... skipping.
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
```

The mysql-secure-installation script will guide you through several key steps:

1. Set a root password if one isn't already set.
2. Remove anonymous users.
3. Disallow remote root logins.
4. Remove the test database.
5. Reload the privilege tables to ensure the changes take effect.

Once complete, your MySQL instance will be significantly more secure.

---

### Create the Zabbix databases

With MySQL now set up and secured, we can move on to creating the database for Zabbix.
This database will store all the necessary data related to your Zabbix server,
including configuration information and monitoring data.

Follow these steps to create the Zabbix database:

1. Log in to the MySQL shell as the root user:

```bash
# mysql -u root -p
```

You'll be prompted to enter the root password that you set during the
mysql-secure-installation process.

Once you're logged into the MySQL shell, run the following command to create
a database for Zabbix:
```sql
mysql> CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
```
This command creates a new database named zabbix with the UTF-8 character set,
which is required for Zabbix.

- Create a dedicated user for Zabbix and grant the necessary privileges:
Next, you need to create a user that Zabbix will use to access the database.
Replace password with a strong password of your choice.

```sql
mysql> CREATE USER 'zabbix-web'@'<zabbix server ip>' IDENTIFIED BY '<password>';
mysql> CREATE USER 'zabbix-srv'@'<zabbix server ip>' IDENTIFIED BY '<password>';
mysql> GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix-srv'@'<zabbix server ip>';
mysql> GRANT SELECT, UPDATE, DELETE, INSERT ON zabbix.* TO 'zabbix-web'@'<zabbix server ip>';
mysql> FLUSH PRIVILEGES;
```

This creates new users for zabbix-web and zabbix-srv, grants them  access to the
zabbix database, and ensures that the privileges are applied immediately.

- In some cases, especially when setting up Zabbix with MariaDB, you might
encounter issues related to stored functions and triggers if binary logging is
enabled.

To address this, you need to set the log_bin_trust_function_creators option to 1
in the MariaDB configuration file.
This allows non-root users to create stored functions and triggers without
requiring SUPER privileges, which are restricted when binary logging is enabled.

```sql
mysql> SET GLOBAL log_bin_trust_function_creators = 1;
mysql> QUIT
```

At this point, your Zabbix database is ready, and you can proceed with configuring
the Zabbix server to connect to the database.

???+ warning
    In the Zabbix documentation, it is explicitly stated that **deterministic
    triggers** need to be created during the schema import.
    On MySQL and MariaDB systems, this requires setting `GLOBAL log_bin_trust_function_creators = 1`
    if binary logging is enabled, and you lack superuser privileges.

    If the `log_bin_trust_function_creators` option is not set in the MySQL
    configuration file, it will block the creation of these triggers during
    schema import. This is essential because, without superuser access, non-root
    users cannot create triggers or stored functions unless this setting is applied.

    To summarize:

    - **Binary logging enabled:** If binary logging is enabled and the user does
    not have superuser privileges, the creation of necessary Zabbix triggers will
    fail unless `log_bin_trust_function_creators = 1` is set.
    - **Solution:** Add `log_bin_trust_function_creators = 1` to the `[mysqld]`
    section in your MySQL/MariaDB configuration file or temporarily set it at
    runtime with `SET GLOBAL log_bin_trust_function_creators = 1` if you have
    sufficient permissions.

    This ensures that Zabbix can successfully create the required triggers during
    schema import without encountering privilege-related errors.

---

### Add the Zabbix repository and populate the MySQL DB

```bash
# rpm -Uvh https://repo.zabbix.com/zabbix/6.5/rocky/9/x86_64/zabbix-release-6.5-2.el9.noarch.rpm
# dnf clean all
# dnf install zabbix-sql-scripts
```

Now let;s upload the data from zabbix (db structure, images, user, ... )

```bash
# zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uroot -p zabbix
Enter password:
```

???+ warning
    Depending on the speed of your hardware or virtual machine, the process may
    take anywhere from a few seconds to several minutes. Please be patient and
    avoid canceling the operation; just wait for the prompt to appear.

Log back into your MySQL Database as root

```bash
# mysql -uroot -p
```

Once the import of the Zabbix schema is complete and you no longer need the
`log_bin_trust_function_creators` global parameter, it is a good practice to
remove it for security reasons.

To revert the change and set the global parameter back to `0`, use the following
command in the MariaDB shell:

```sql
mysql> SET GLOBAL log_bin_trust_function_creators = 0;
Query OK, 0 rows affected (0.001 sec)
```

This command will disable the setting, ensuring that the server's security posture
remains robust.

---

### Configure the firewall

Configuring Firewall Rules for MySQL Access
To complete our database setup, we need to configure the firewall to allow incoming
connections for the MySQL database from our Zabbix server. Currently, no
connections are accepted. Let's examine the current firewall configuration:

```bash
# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client  ssh
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

To securely allow MySQL access, we'll create a dedicated zone and open port 3306/tcp
only for the Zabbix server's IP address:

- Create a new firewall zone for MariaDB:

```bash
# firewall-cmd --new-zone=mysql-access --permanent
success
```
- Reload the firewall to apply changes:

```bash
# firewall-cmd --reload
success
```

- Verify the new zone has been added:

```bash
# firewall-cmd --get-zones
block dmz drop external home internal mysql-access nm-shared public trusted work
```

- Add the Zabbix server's IP to the new zone:

```bash
# firewall-cmd --zone=mysql-access --add-source=<zabbix-serverip> --permanent
```

- Open port 3306/tcp in the new zone:

```bash
success
# firewall-cmd --zone=mysql-access --add-port=3306/tcp  --permanent
```

- Reload the firewall again to apply the latest changes:

```bash
success
# firewall-cmd --reload
```

To verify the new firewall rules, inspect the mysql-access zone:

```bash
# firewall-cmd --list-all --zone=mysql-access
```

```bash
mysql-access (active)
  target: default
  icmp-block-inversion: no
  interfaces:
  sources: <ip from the zabbix-server>
  services:
  ports: 3306/tcp
  protocols:
  forward: no
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

With these configurations in place, the database server is now ready to accept
connections from the Zabbix server. You can proceed with the next
task: [Installing the Zabbix Server](installing-zabbix.md)

---

## Installing Zabbix with PostgreSQL

For our DB setup with PostgreSQL we need to add our PostgreSQL repository first to the system. As of writing PostgreSQL 13-16 are supported but best is to have a look before you install it as new versions may be supported and older maybe unsupported both by Zabbix and PostgreSQL. Usually it's a good idea to go with the latest version that is supported by Zabbix. Zabbix also supports the extension TimescaleDB this is someting we will talk later about. As you will see the setup from PostgreSQL is very different from MySQL not only the installation but also securing the DB.

The table of compatibility can be found [here](https://docs.timescale.com/self-hosted/latest/upgrades/upgrade-pg/).

### Add the PostgreSQL repo

So let us start first setting up our PostgreSQL repository with the folowing commands.

```
# Install the repository RPM:
sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-9-x86_64/pgdg-redhat-repo-latest.noarch.rpm

# Disable the built-in PostgreSQL module:
sudo dnf -qy module disable postgresql

# Install PostgreSQL:
sudo dnf install -y postgresql16-server

# Initialize the database and enable automatic start:
sudo /usr/pgsql-16/bin/postgresql-16-setup initdb
sudo systemctl enable postgresql-16 --now
```

### Securing the PostgreSQL database

As i told you PostgreSQL works a bit different then MySQL or MariaDB and this applies aswell to how we manage access permissions. Postgres works with a file with the name pg_hba.conf where we have to tell who can access our database from where and what encryption is used for the password. So let's edit this file to allow our frontend and zabbix server to access the database.

???+ note
"Client authentication is configured by a configuration file with the name `pg_hba.conf`. HBA here stands for host based authentication. For more information feel free to check the [PostgreSQL documentation](https://www.postgresql.org/docs/current/auth-pg-hba-conf.html)."

Add the following lines, the order here is important.

```
# vi /var/lib/pgsql/16/data/pg_hba.conf
```

```
# "local" is for Unix domain socket connections only
local   zabbix          zabbix-srv                               scram-sha-256
local   all             all                                      peer
# IPv4 local connections:
host    zabbix          zabbix-srv      <ip from zabbix server/24> scram-sha-256
host    zabbix          zabbix-web      <ip from zabbix server/24> scram-sha-256
host    all             all             127.0.0.1/32             scram-sha-256
```

After we changed the pg_hba file don't forget to restart postgres else the settings will not be applied. But before we restart let us also edit the file postgresql.conf and allow our database to listen on our network interface for incomming connections from the zabbix server. Postgresql will standard only allow connections from the socket.

```
# vi /var/lib/pgsql/16/data/postgresql.conf
```

and replace the line with listen_addresses so that PostgreSQL will listen on all interfaces and not only on our localhost.

```
#listen_addresses = 'localhost' with  listen_addresses = '*'
```

When done restart the PostgreSQL cluster and see if it comes back online in case of an error check the `pg_hba.conf` file you just edited for typos.

```
# systemctl restart postgresql-16
```

For our Zabbix server we need to create tables in the database for this we need ot install the Zabbix repository like we did for our Zabbix server and install the Zabbix package containing all the database tables images icons, ....

### Add the Zabbix repository and populate the DB

```
# dnf install https://repo.zabbix.com/zabbix/6.0/rhel/9/x86_64/zabbix-release-6.0-4.el9.noarch.rpm -y
# dnf install zabbix-sql-scripts -y
```

Now we are ready to create our Zabbix users for the server and the frontend:

```
# su - postgres
# createuser --pwprompt zabbix-srv
Enter password for new role: <server-password>
Enter it again: <server-password>
```

Let's do the same for our frontend let's create a user to connect to the database:

```
# createuser --pwprompt zabbix-web
Enter password for new role: <frontend-password>
Enter it again: <frontend-password>
```

Next we have to unzip the database schema files. Run as user root followin command::

```
# gzip -d /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz
```

We are now ready to create our database zabbix. Become user postgres again and run next command to create the database as our user zabbix-srv:

```
# su - postgres
# createdb -E Unicode -O zabbix-srv  zabbix
```

Let's verify that we are really connected to the database with the correct session.
Login from the Postgres shell on the zabbix database

```
# psql -d zabbix -U zabbix-srv
```

Make sure we are logged in with our correct user `zabbix-srv`.

```
zabbix=> SELECT session_user, current_user;
 session_user | current_user
--------------+--------------
 zabbix-srv   | zabbix-srv
(1 row)
```

PostgreSQL works a bit different then MySQL or MariaDB when it comes to almost everything :) One of the things that it has that MySQL not has are for example shemas. If you like to know more about it i can recommend [this](https://hevodata.com/learn/postgresql-schema/#schema) URI. It explains in detail what it is and why we need it. But in short ... In PostgreSQL schema enables a multi-user environment that allows multiple users to access the same database without interference. Schemas are important when several users use the application and access the database in their way or when various applications utilize the same database. There is a standard schema that you can use but the better way is to create our own schema.

???+ note
"There is a standard schema `public` that you can use but the better way is to create our own schema this was if later something else is installed next to the Zabbix database it will be easier to create users with only access to the newly created database tables."

```
zabbix=> CREATE SCHEMA zabbix_server AUTHORIZATION "zabbix-srv";
CREATE SCHEMA
zabbix=> set search_path to "zabbix_server";
zabbix=> \dn
          List of schemas
     Name      |       Owner
---------------+-------------------
 public        | pg_database_owner
 zabbix_server | zabbix-srv
(2 rows)


```

Now we have our DB ready with correct permissions for user `zabbix-srv` but not yet for our user `zabbix-web`. Let's fix this first and give the rights to connect to our schema.

```
zabbix=# GRANT USAGE ON SCHEMA zabbix_server TO "zabbix-web";
GRANT
```

The user `zabbix-web` has now the rights to connect to our schema but cannot to anything yet lets fix this but also don't give too many rights.

```
zabbix=# GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA zabbix_server TO "zabbix-web";
GRANT
zabbix=# GRANT SELECT, UPDATE ON ALL SEQUENCES IN SCHEMA zabbix_server TO "zabbix-web";
GRANT
```

There we go both users are created with the correct permissons.
We are now ready to populate the database with the Zabbix table structures etc ... log back in as user postgres and run the following commands

Let's upload the Zabbix SQL file we extracted earlier to populate our database with the needed schemas images users etc ...

???+ warning
"Depending on the speed of your hardware or VM this can take a few seconds upto a few minutes so please don't cancel just sit and wait for the prompt."

```
zabbix=# \i /usr/share/zabbix-sql-scripts/postgresql/server.sql
CREATE TABLE
CREATE INDEX
...
...
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
COMMIT
zabbix=#
```

???+ note
"If the import fails with `psql:/usr/share/zabbix-sql-scripts/postgresql/server.sql:7: ERROR:  no schema has been selected to create in` then you probably made an error in the line where you set the search path."

Lets verify that our tables are properly created with the correct permissions

```
zabbix=# \dt
                        List of relations
    Schema     |            Name            | Type  |   Owner
---------------+----------------------------+-------+------------
 zabbix_server | acknowledges               | table | zabbix-srv
 zabbix_server | actions                    | table | zabbix-srv
 zabbix_server | alerts                     | table | zabbix-srv
 zabbix_server | auditlog                   | table | zabbix-srv
 zabbix_server | autoreg_host               | table | zabbix-srv
...
...
 zabbix_server | usrgrp                     | table | zabbix-srv
 zabbix_server | valuemap                   | table | zabbix-srv
 zabbix_server | valuemap_mapping           | table | zabbix-srv
 zabbix_server | widget                     | table | zabbix-srv
 zabbix_server | widget_field               | table | zabbix-srv
(173 rows)
```

???+ note
"If you are like me and don't like to set the search path every time you logon with the user zabbix-srv to the correct search path you can run the following SQL. `zabbix=> alter role "zabbix-srv" set search_path = "$user", public, zabbix_server ;`"

If you are ready you can exit the database and return as user root.

```
zabbix=>  \q
# exit
```

### Configure the firewall

One last thing we need to do is open the firewall and allow incoming connections for the PostgreSQL database from our Zabbix server because at the moment we dont accept any connections yet.

```
# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client  ssh
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

First we will create an appropriate zone for our PostgreSQL DB and open port 5432/tcp but only for the ip from our Zabbix server.

```
# firewall-cmd --new-zone=postgresql-access --permanent
success

# firewall-cmd --reload
success

# firewall-cmd --get-zones
block dmz drop external home internal nm-shared postgresql-access public trusted work

# firewall-cmd --zone=postgresql-access--add-source=<zabbix-serverip> --permanent

success
# firewall-cmd --zone=postgresql-access --add-port=5432/tcp  --permanent

success
# firewall-cmd --reload
```

Now lets have a look to our firewall rules to see if they are what we expected:

```
# firewall-cmd --zone=postgresql-access --list-all
```

```
postgresql-access (active)
  target: default
  icmp-block-inversion: no
  interfaces:
  sources: 192.168.56.18
  services:
  ports: 5432/tcp
  protocols:
  forward: no
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

Our database server is ready now to accept connections from our Zabbix server :).
You can continue with the next task [Installing the Zabbix Server](installing-zabbix.md)
