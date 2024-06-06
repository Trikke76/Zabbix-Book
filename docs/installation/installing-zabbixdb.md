# Installing Zabbix DB Server

In this chapter we will install our Zabbix database. Zabbix support a wide range of SQL databases but we will limit us to PostgreSQL, MariaDB and MySQL for now.
There is also Oracle that is supported but support for Oracle is deprectated, Zabbix 7 will be the last version of Zabbix supporting Oracle as backend.
In this Chapter we will explain how to install and configure every database on a seperate machine however you could also install it on your Zabbix server, there is no rule that forces you to install the DB on your Zabbix server or on a seperate server.

For most setups a DB on a local machine will probably be enough to start with, just make sure the DB is on other disks then the OS. If later performance is an issue you can still move the DB to another server. Don't forget that local connections are faster then connections over TCP so there it's not always best to move the DB to it's own server it all depends on your needs.



## Installing Zabbix with MariaDB


Let us start with the installation of the MariaDB server, you need to create a MariaDB repository configuration file `mariadb.repo` manually in the following path `/etc/yum.repos.d/`.
To create a MariaDB repository file, you can use the following command.


### Add the MariaDB repo

```
# vi /etc/yum.repos.d/mariadb.repo
```

The above command will create a new repository file, Once it is created, you need to add the following configuration into the file.
Make sure your version, in this case 10.11, is supported by Zabbix by looking at the latest [requirements](https://www.zabbix.com/documentation/current/en/manual/installation/requirements) for your version. 

```
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
Lets update our OS first with the latest patches

```
# dnf update -y
```
### Install the MariaDB database

Now we are ready to install our MariaDB database.

```
# dnf install MariaDB-server MariaDB-client
```

We are now ready to enable and start or MariaDB database.
```
# systemctl enable mariadb --now
```
Once the installation is complete, you can verify the version of the MariaDB server by using the following command:

```
# mysql -V
```

The output should look like this:

```
mysql  Ver 15.1 Distrib 10.11.6-MariaDB, for Linux (x86_64) using  EditLine wrapper
```

And when we ask the status of our MariaDB server we should get an output like this:

```
# systemctl status mariadb

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
### Securing the MariaDB database

It's time to secure our database by removing the test database and user and set our own root password.  Run the command `mariadb-secure-installation`, you should get the following output.

```


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
### Create the Zabbix database 

```
# mysql -uroot -p
password

MariaDB [(none)]> CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
MariaDB [(none)]> CREATE USER 'zabbix-web'@'<zabbix server ip>' IDENTIFIED BY '<password>';
MariaDB [(none)]> CREATE USER 'zabbix-srv'@'<zabbix server ip>' IDENTIFIED BY '<password>';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix-srv'@'<zabbix server ip>';
MariaDB [(none)]> GRANT SELECT, UPDATE, DELETE, INSERT ON zabbix.* TO 'zabbix-web'@'<zabbix server ip>';
MariaDB [(none)]> SET GLOBAL log_bin_trust_function_creators = 1;
MariaDB [(none)]> QUIT

```

???+ warning 
    "The Zabbix documentation explicitly mentions that deterministic triggers need to be created during the import of schema. On MySQL and MariaDB, this requires GLOBAL log_bin_trust_function_creators = 1 to be set if binary logging is enabled and there is no superuser privileges and log_bin_trust_function_creators = 1 is not set in MySQL configuration file."


### Add the Zabbix repository and populate the DB

```
# rpm -Uvh https://repo.zabbix.com/zabbix/6.5/rocky/9/x86_64/zabbix-release-6.5-2.el9.noarch.rpm
# dnf clean all
# dnf install zabbix-sql-scripts
```
Upload the data from zabbix (db structure, images, user, ... )

```
# zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uroot -p zabbix
```

???+ warning
    "Depending on the speed of your hardware or VM this can take a few seconds upto a few minutes so please don't cancel just sit and wait for the prompt."

Log back into your MariaDB Database as root

```
# mysql -uroot -p
```
Remove the global parameter again as its not needed anymore and also for security reasons.

```
MariaDB [(none)]> SET GLOBAL log_bin_trust_function_creators = 0;
Query OK, 0 rows affected (0.001 sec)
```

### Configure the firewall

One last thing we need to do is open the firewall and allow incoming connections for the MariaDB database from our Zabbix server because at the moment we dont accept any connections yet.

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

First we will create an appropriate zone for our MariaDB and open port 3306/tcp but only for the ip from our Zabbix server.

```
# firewall-cmd --new-zone=mariadb-access --permanent
success

# firewall-cmd --reload
success

# firewall-cmd --get-zones
block dmz drop external home internal mariadb-access nm-shared public trusted work

# firewall-cmd --zone=mariadb-access --add-source=<zabbix-serverip> --permanent

success
# firewall-cmd --zone=mariadb-access --add-port=3306/tcp  --permanent

success
# firewall-cmd --reload
```

Now lets have a look to our firewall rules to see if they are what we expected:

```
# firewall-cmd --zone=mariadb-access --list-all
```

```
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

Our database server is ready now to accept connections from our Zabbix server :).
You can continue with the next task [Installing the Zabbix Server](installing-zabbix.md)



## Installing Zabbix with MySQL


Let us start with the installation of the MySQL server, you need to create a MySQL repository first so that we can install the proper files for our MySQL server
It's alwqys best to check the Zabbix [documentation](https://www.zabbix.com/documentation/current/en/manual/installation/requirements) to see what version is supported so you don't install a version that is not supported or is not supported anymore.

### Add the MySQL repo

Run the following command to install the MySQL repo for version 8.0

```# dnf -y install https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm```

???+ note
    "If you install this on RedHat 8 and higher or alternatives like CentOS, Rocky or Alma 8 then you need to disable the mysql module by running 'module disable mysql'."

Let's update our OS first with the latest patches

```# dnf update -y```

#### Installing the MySQL database

```# dnf -y install mysql-community-server ```

We are now ready to enable and start or MySQL database.

```# systemctl enable mysqld --now```

Once the installation is complete, you can verify the version of the MySQL server by using the following command:

```# mysql -V```

The output should look like this:

```mysql  Ver 8.0.35 for Linux on x86_64 (MySQL Community Server - GPL)```

And when we ask the status of our MariaDB server we should get an output like this:

```
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
### Securing the MySQL database

MySQL will secure our database with a random root password that is generated when we install the database. First thing we need to do is replace it with our own password. To find what the password is we need to read the log file with the followin command:

```# grep 'temporary password' /var/log/mysqld.log```

Change the root password as soon as possible by logging in with the generated, temporary password and set a custom password for the superuser account:
```
# mysql -uroot -p
```
```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '<my mysql password>';
mysql> quit
```
Next we can run the command mysql_secure_installation, you should get the following output:

???+ note
    "There is no need to reset the root password for MySQL again as we have reset it already. The next step is optional but recommended."

```
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
Let's create our DB users and the correct permissions in the database:

```mysql -uroot -p```

```
mysql> CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
mysql> CREATE USER 'zabbix-web'@'<zabbix server ip>' IDENTIFIED BY '<password>';
mysql> CREATE USER 'zabbix-srv'@'<zabbix server ip>' IDENTIFIED BY '<password>';
mysql> GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix-srv'@'<zabbix server ip>';
mysql> GRANT SELECT, UPDATE, DELETE, INSERT ON zabbix.* TO 'zabbix-web'@'<zabbix server ip>';
mysql> SET GLOBAL log_bin_trust_function_creators = 1;
mysql> QUIT
```

???+ warning
    "The Zabbix documentation explicitly mentions that deterministic triggers need to be created during the import of schema. On MySQL and MariaDB, this requires GLOBAL log_bin_trust_function_creators = 1 to be set if binary logging is enabled and there is no superuser privileges and log_bin_trust_function_creators = 1 is not set in MySQL configuration file."

### Add the Zabbix repository and populate the DB

```
# rpm -Uvh https://repo.zabbix.com/zabbix/6.5/rocky/9/x86_64/zabbix-release-6.5-2.el9.noarch.rpm
# dnf clean all
# dnf install zabbix-sql-scripts

```
Now let;s upload the data from zabbix (db structure, images, user, ... )

```
# zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uroot -p zabbix
Enter password:
```

???+ warning
    "Depending on the speed of your hardware or VM this can take a few seconds upto a few minutes so please don't cancel just sit and wait for the prompt."

```
Log back into your MySQL Database as root

# mysql -uroot -p
```

Remove the global parameter again as its not needed anymore and also for security reasons.

```
mysql> SET GLOBAL log_bin_trust_function_creators = 0;
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

### Configure the firewall
One last thing we need to do is open the firewall and allow incoming connections from our Zabbix server to our MySQL database because at the moment we dont accept any connections yet.

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

First we will create an appropriate zone for our MySQL Database and open port 3306/tcp but only for the IP from our Zabbix server. This way no one unallowed is able to connect.

```
# firewall-cmd --new-zone=mysql-access --permanent
success

# firewall-cmd --reload
success

# firewall-cmd --get-zones
block dmz drop external home internal mysql-access nm-shared public trusted work

# firewall-cmd --zone=mysql-access --add-source=<zabbix-serverip> --permanent

success
# firewall-cmd --zone=mysql-access --add-port=3306/tcp  --permanent

success
# firewall-cmd --reload
```

Now lets have a look to our firewall rules to see if they are what we expected:

```
# firewall-cmd --list-all --zone=mysql-access
```

```
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

Our database server is ready now to accept connections from our Zabbix server :).
You can continue with the next task [Installing the Zabbix Server](installing-zabbix.md)


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
    "Client authentication is configured by a configuration file with the name ```pg_hba.conf```. HBA here stands for host based authentication. For more information feel free to check the [PostgreSQL documentation](https://www.postgresql.org/docs/current/auth-pg-hba-conf.html)."

Add the following lines, the order here is important.

```
# vi /var/lib/pgsql/16/data/pg_hba.conf
```

```
# "local" is for Unix domain socket connections only
local   zabbix          zabbix-srv                              	scram-sha-256
local   all             all                                     	peer
# IPv4 local connections:
host    zabbix          zabbix-srv      <ip from zabbix server/24>	scram-sha-256
host    zabbix          zabbix-web      <ip from zabbix server/24>	scram-sha-256
host    all             all             127.0.0.1/32            	scram-sha-256
```
After we changed the pg_hba file don't forget to restart postgres else the settings will not be applied. But before we restart let us also edit the file postgresql.conf and allow our database to listen on our network interface for incomming connections from the zabbix server. Postgresql will standard only allow connections from the socket.

```
# vi /var/lib/pgsql/16/data/postgresql.conf
```
and replace the line with listen_addresses so that PostgreSQL will listen on all interfaces and not only on our localhost.

```
#listen_addresses = 'localhost' with  listen_addresses = '*'
```

When done restart the PostgreSQL cluster and see if it comes back online in case of an error check the ```pg_hba.conf``` file you just edited for typos.

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
Make sure we are logged in with our correct user ```zabbix-srv```.

```
zabbix=> SELECT session_user, current_user;
 session_user | current_user
--------------+--------------
 zabbix-srv   | zabbix-srv
(1 row)
```

PostgreSQL works a bit different then MySQL or MariaDB when it comes to almost everything :) One of the things that it has that MySQL not has are for example shemas. If you like to know more about it i can recommend [this](https://hevodata.com/learn/postgresql-schema/#schema)  URI. It explains in detail what it is and why we need it. But in short ...  In PostgreSQL schema enables a multi-user environment that allows multiple users to access the same database without interference. Schemas are important when several users use the application and access the database in their way or when various applications utilize the same database. There is a standard schema that you can use but the better way is to create our own schema.

???+ note
    "There is a standard schema ```public``` that you can use but the better way is to create our own schema this was if later something else is installed next to the Zabbix database it will be easier to create users with only access to the newly created database tables."

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
Now we have our DB ready with correct permissions for user ```zabbix-srv``` but not yet for our user ```zabbix-web```. Let's fix this first and give the rights to connect to our schema.

```
zabbix=# GRANT USAGE ON SCHEMA zabbix_server TO "zabbix-web";
GRANT
```

The user ```zabbix-web``` has now the rights to connect to our schema but cannot to anything yet lets fix this but also don't give too many rights.

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
    "If the import fails with ```psql:/usr/share/zabbix-sql-scripts/postgresql/server.sql:7: ERROR:  no schema has been selected to create in```  then you probably made an error in the line where you set the search path."

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
    "If you are like me and don't like to set the search path every time you logon with the user zabbix-srv to the correct search path you can run the following SQL. ```zabbix=> alter role "zabbix-srv" set search_path = "$user", public, zabbix_server ;```"


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

