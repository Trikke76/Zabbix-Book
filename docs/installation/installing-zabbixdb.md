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

- Log in to the MariaDB shell as the root user:

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

As you've pointed out, PostgreSQL handles access permissions differently from
MySQL and MariaDB. PostgreSQL relies on a file called pg_hba.conf to manage who
can access the database, from where, and what encryption method is used for authentication.

???+ note
    Client authentication in PostgreSQL is configured through the pg_hba.conf file,
    where "HBA" stands for Host-Based Authentication. This file specifies which
    users can access the database, from which hosts, and how they are authenticated.
    For further details, you can refer to the official [PostgreSQL documentation](https://www.postgresql.org/docs/current/auth-pg-hba-conf.html)."

Add the following lines, the order here is important.

```bash
# vi /var/lib/pgsql/16/data/pg_hba.conf
```

```sql
# "local" is for Unix domain socket connections only
local   zabbix          zabbix-srv                               scram-sha-256
local   all             all                                      peer
# IPv4 local connections:
host    zabbix          zabbix-srv      <ip from zabbix server/24> scram-sha-256
host    zabbix          zabbix-web      <ip from zabbix server/24> scram-sha-256
host    all             all             127.0.0.1/32             scram-sha-256
```

After we changed the pg_hba file don't forget to restart postgres else the settings
will not be applied.
But before we restart let us also edit the file postgresql.conf and allow our database
to listen on our network interface for incomming connections from the zabbix server.
Postgresql will standard only allow connections from the socket.

```bash
# vi /var/lib/pgsql/16/data/postgresql.conf
```

To configure PostgreSQL to listen on all network interfaces, you need to modify
the `postgresql.conf` file. Locate the following line:

```bash
#listen_addresses = 'localhost'
```

Replace it with:

```bash
listen_addresses = '*'
```

This will enable PostgreSQL to accept connections from any network interface,
not just the local machine. After making this change, restart the PostgreSQL
service to apply the new settings:

```bash
# systemctl restart postgresql-16
```

If the service fails to restart, review the `pg_hba.conf` file for any syntax errors,
as incorrect entries here may prevent PostgreSQL from starting.

Next, to prepare your PostgreSQL instance for Zabbix, you’ll need to create the
necessary database tables. Begin by installing the Zabbix repository, as you did
for the Zabbix server.
Then, install the appropriate Zabbix package that contains the predefined tables,
images, icons, and other database elements needed for the Zabbix application.

---

### Add the Zabbix repository and populate the PgSQL DB

To begin, add the Zabbix repository to your system by running the following commands:

```bash
# dnf install https://repo.zabbix.com/zabbix/6.0/rhel/9/x86_64/zabbix-release-6.0-4.el9.noarch.rpm -y
# dnf install zabbix-sql-scripts -y
```

With the necessary packages installed, you are now ready to create the Zabbix
users for both the server and frontend.

First, switch to the `postgres` user and create the Zabbix server database user:

```bash
# su - postgres
# createuser --pwprompt zabbix-srv
Enter password for new role: <server-password>
Enter it again: <server-password>
```

Next, create the Zabbix frontend user, which will be used to connect to the database:

```bash
# createuser --pwprompt zabbix-web
Enter password for new role: <frontend-password>
Enter it again: <frontend-password>
```

After creating the users, you need to prepare the database schema. As the root user,
unzip the necessary schema files by running the following command:

```bash
# gzip -d /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz
```

This will extract the database schema required for the Zabbix server.

Now that the users are created, the next step is to create the Zabbix database.
First, switch to the `postgres` user and execute the following command to
create the database with the owner set to `zabbix-srv`:

```bash
# su - postgres
# createdb -E Unicode -O zabbix-srv zabbix
```

Once the database is created, you should verify the connection and ensure that
the correct user session is active. To do this, log into the `zabbix` database
using the `zabbix-srv` user:

```bash
# psql -d zabbix -U zabbix-srv
```

After logging in, run the following SQL query to confirm that both the `session_user`
and `current_user` are set to `zabbix-srv`:

```sql
zabbix=> SELECT session_user, current_user;
 session_user | current_user
--------------+--------------
 zabbix-srv   | zabbix-srv
(1 row)
```

If the output matches, you are successfully connected to the database with the correct user.

We are now ready to create our database zabbix. Become user postgres again and
run next command to create the database as our user zabbix-srv:

```bash
# su - postgres
# createdb -E Unicode -O zabbix-srv  zabbix
```

Let's verify that we are really connected to the database with the correct session.
Login from the Postgres shell on the zabbix database

```bash
# psql -d zabbix -U zabbix-srv
```

Make sure we are logged in with our correct user `zabbix-srv`.

```sql
zabbix=> SELECT session_user, current_user;
 session_user | current_user
--------------+--------------
 zabbix-srv   | zabbix-srv
(1 row)
```

PostgreSQL indeed differs significantly from MySQL or MariaDB in several aspects,
and one of the key features that sets it apart is its use of schemas. Unlike MySQL,
where databases are more standalone, PostgreSQL's schema system provides a structured,
multi-user environment within a single database.

Schemas act as logical containers within a database, enabling multiple users or
applications to access and manage data independently without conflicts.
This feature is especially valuable in environments where several users or
applications need to interact with the same database concurrently. Each user or
application can have its own schema, preventing accidental interference with each
other’s data.

???+ note
    PostgreSQL comes with a default schema, typically called `public`, but it's
    generallya best practice to create custom schemas to better organize and
    separate database objects, especially in complex or multi-user environments.

    For more in-depth information, I recommend checking out the detailed guide
    at [this URI](https://hevodata.com/learn/postgresql-schema/#schema), which
    explains the benefits and use cases for schemas in PostgreSQL.

To finalize the database setup for Zabbix, we need to configure schema permissions
for both the `zabbix-srv` and `zabbix-web` users.

First, we create a custom schema named `zabbix_server` and assign ownership to
the `zabbix-srv` user:

```sql
zabbix=> CREATE SCHEMA zabbix_server AUTHORIZATION "zabbix-srv";
CREATE SCHEMA
```

Next, we set the search path to the `zabbix_server` schema so that it's the
default for the current session:

```sql
zabbix=> SET search_path TO "zabbix_server";
```

To confirm the schema setup, you can list the existing schemas:

```sql
zabbix=> \dn
          List of schemas
     Name      |       Owner
---------------+-------------------
 public        | pg_database_owner
 zabbix_server | zabbix-srv
(2 rows)
```

At this point, the `zabbix-srv` user has full access to the schema, but the
`zabbix-web` user still needs appropriate permissions to connect and interact
with the database. First, we grant `USAGE` privileges on the schema to allow
`zabbix-web` to connect:

```sql
zabbix=# GRANT USAGE ON SCHEMA zabbix_server TO "zabbix-web";
GRANT
```

However, `zabbix-web` still cannot perform any operations on the tables or
sequences. To allow basic data interaction without giving too many privileges,
grant the following permissions:

- For tables: `SELECT`, `INSERT`, `UPDATE`, and `DELETE`.
- For sequences: `SELECT` and `UPDATE`.

```sql
zabbix=# GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA zabbix_server TO "zabbix-web";
GRANT
```

```sql
zabbix=# GRANT SELECT, UPDATE ON ALL SEQUENCES IN SCHEMA zabbix_server TO "zabbix-web";
GRANT
```

Now, the `zabbix-web` user has appropriate access to interact with the schema
while maintaining security by limiting permissions to essential operations.

With the users and permissions set up correctly, you can now populate the database
with the Zabbix schema and other required elements. Follow these steps:

1. **Log in as the `postgres` user** if you are not already:

   ```bash
   # su - postgres
   ```

2. **Connect to the Zabbix database** and run the SQL script to create the necessar
y tables and insert initial data. Enter the `psql` shell for the `zabbix` database:

   ```bash
   # psql -d zabbix -U zabbix-srv
   ```

3. **Execute the SQL file** to populate the database. Run the following command
in the `psql` shell:

   ```sql
   zabbix=# \i /usr/share/zabbix-sql-scripts/postgresql/server.sql
   ```

   **Warning:** Depending on your hardware or VM performance, this process can
   take anywhere from a few seconds to several minutes.
   Please be patient and avoid canceling the operation.

4. **Monitor the progress** as the script runs. You will see output similar to:

   ```plaintext
   CREATE TABLE
   CREATE INDEX
   ...
   ...
   INSERT 0 1
   INSERT 0 1
   INSERT 0 1
   INSERT 0 1
   COMMIT
   ```

   Once the script completes and you return to the `zabbix=#` prompt, the database
   should be successfully populated with all the required tables, schemas, images,
   and other elements needed for Zabbix.

???+ note
    If you encounter the following error during the SQL import:
    ```vbnet
    psql:/usr/share/zabbix-sql-scripts/postgresql/server.sql:7: ERROR: no schema has been selected to create in
    ```
    It indicates that the search_path setting might not have been correctly applied.
    This setting is crucial because it specifies the schema where the tables and
    other objects should be created.
    By correctly setting the search path, you ensure that the SQL script will
    create tables and other objects in the intended schema.

To ensure that the Zabbix tables were created successfully and have the correct
permissions, you can verify the table list and their ownership using the
`psql` command:

- **List the Tables**: Use the following command to list all tables in the `zabbix_server` schema:

   ```sql
   zabbix=# \dt
   ```

   You should see a list of tables with their schema, name, type, and owner. For example:

```bash
Schema         | Name                       | Type  | Owner
----------------+----------------------------+-------+------------
zabbix_server  | acknowledges               | table | zabbix-srv
zabbix_server  | actions                    | table | zabbix-srv
zabbix_server  | alerts                     | table | zabbix-srv
zabbix_server  | auditlog                   | table | zabbix-srv
zabbix_server  | autoreg_host               | table | zabbix-srv
...
zabbix_server  | usrgrp                      | table | zabbix-srv
zabbix_server  | valuemap                    | table | zabbix-srv
zabbix_server  | valuemap_mapping            | table | zabbix-srv
zabbix_server  | widget                      | table | zabbix-srv
zabbix_server  | widget_field                | table | zabbix-srv
(173 rows)
```

- **Verify Permissions**: Confirm that the `zabbix-srv` user owns the tables and
has the necessary permissions. You can check permissions for specific tables
using the `\dp` command:

   ```sql
   zabbix=# \dp zabbix_server.*
   ```

   This will display the access privileges for all tables in the `zabbix_server`
   schema. Ensure that `zabbix-srv` has the required privileges.

If everything looks correct, your tables are properly created and the `zabbix-srv`
user has the appropriate ownership and permissions. If you need to adjust any
permissions, you can do so using the `GRANT` commands as needed.

???+ note
    If you prefer not to set the search path manually each time you log in as the
    `zabbix-srv` user, you can configure PostgreSQL to automatically use the desired
    search path. Run the following SQL command to set the default search path for
    the `zabbix-srv` role:

    ```sql
    zabbix=> ALTER ROLE "zabbix-srv" SET search_path = "$user", public, zabbix_server;
    ```

    This command ensures that every time the `zabbix-srv` user connects to the
    database, the `search_path` is automatically set to include `$user`, `public`,
    and `zabbix_server`.

If you are ready you can exit the database and return as user root.

```bash
zabbix=>  \q
# exit
```

### Configure the firewall

To allow incoming connections to the PostgreSQL database from your Zabbix server,
follow these steps to configure the firewall:

1. **Check Current Firewall Configuration**: Verify the current settings to ensure
the firewall is active and no PostgreSQL-related rules are present:

   ```bash
   # firewall-cmd --list-all
   ```

2. **Create a New Zone for PostgreSQL**: Define a new firewall zone specifically
for PostgreSQL access:

   ```bash
   # firewall-cmd --new-zone=postgresql-access --permanent
   success
   ```

3. **Reload Firewall Configuration**: Apply the changes made to the firewall configuration:

   ```bash
   # firewall-cmd --reload
   success
   ```

4. **Verify New Zones**: Check that the new `postgresql-access` zone has been created:

   ```bash
   # firewall-cmd --get-zones
   block dmz drop external home internal nm-shared postgresql-access public trusted work
   ```

5. **Allow Zabbix Server IP**: Add the IP address of your Zabbix server to the
new `postgresql-access` zone to permit incoming connections:

   ```bash
   # firewall-cmd --zone=postgresql-access --add-source=<zabbix-serverip> --permanent
   success
   ```

6. **Open PostgreSQL Port**: Open port `5432/tcp` for the PostgreSQL service within
the `postgresql-access` zone:

   ```bash
   # firewall-cmd --zone=postgresql-access --add-port=5432/tcp --permanent
   success
   ```

7. **Reload Firewall Configuration Again**: Apply the new rules to the firewall:

   ```bash
   # firewall-cmd --reload
   ```

8. **Verify the Firewall Rules**: Ensure that the new rules are correctly applied
by listing the configurations for the `postgresql-access` zone:

   ```bash
   # firewall-cmd --zone=postgresql-access --list-all
   ```

   You should see output similar to:

   ```bash
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

With these steps, the firewall will be configured to allow incoming connections to
the PostgreSQL database from your Zabbix server on port `5432/tcp`.

This concludes our chapter.
You can continue with the next task [Installing the Zabbix Server](installing-zabbix.md)
