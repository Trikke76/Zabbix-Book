# Starting with the basics

## Zabbix Interface

This chapter is going to cover the basics we need to know when it comes to the Zabbix userinterface and the thing we need to know before we can start to fully dive into our monitoring tool. We will see how the userinteface works how to add a host, groups users, items ... so that we have a good understanding of the basics. This is something that is sometimes missed and can lead to frustrations not knowing why things don't work like we had expected them to work. So even if you are an advanced user it may be usefull to have a look into this chapter.


### Overview of the interface

With Zabbix 7 the user interface after logging in is a bit changed. Our menu on the left side of the screen has has a small overhaul. Let's dive into it.
When we login into our Zabbix setup the first time with our Admin user we see a page like this where we have our ```main window``` in green our ```main menu``` marked in red and our ```links``` marked in yellow.

![main view](CH02/main-window.png)

The main menu can be hidden by collapsing it completely or to reduce it to a set of small icons. 


When we click on the button with the 2 arrows to the left:

![Image title](CH02/small-menu.png/){ width="200" } 

You will see that the menu collapses to a set of small icons. Pressing ">>" will bring the ```main menu``` back to it's original state. Pressing the box with the arrow sticking out next to the "<<" button will hide the ```main menu``` completely.

![Image title](CH02/hide-menu.png/){ width="200" }

 To get the main menu back it's not too difficult we just look for the button on the left with three horizontal lines and click it. This will bring the menu back and clicking on the box with the arrow agian will bring the ```main menu``` back.

Yet another way to make the screen bigger that is quit useful for monitors in NOK teams for example is the kiosk mode button. This one is however located on the left side of your screen and looks like 4 arrows pointing to every corner of the screen. Pressing this button will remove all the menus and leave only main window to focus on.

![Image title](CH02/kiosk-menu.png/){ width="70" } ![Image title](CH02/exitkiosk-menu.png/){ width="70" }

When wanting to leave the kios mode the button will be changed to 2 arrows poiting to the inside of the screen. Pressing this button will revert us back to the original state.

Zabbix also has a global search menu that we can use to find hosts, host groups and templates.

![Image title](CH02/search-menu.png/){ width="200" }

If we look in the search box for ```server``` you will see that we get an overview of all templates, host groups and hosts with the name ```server``` in it.

![Image title](CH02/main-search.png/)

Our main menu on the left consists of a few sections, 9 to be exact:


* Dashboards		--> Contains an overview of all the dashboards we have access to
* Monitoring		--> Shows us the hosts, problems, latest data, maps, ...
* Services		--> An overview of all the Services and SLA settings
* Inventory		--> An overview of our collected inventory data
* Reports		--> Shows us the system information, scheduled reports, audit logs, action logs, etc
* Data collection	--> Contains all things related to collecting data like hosts, templates, maintenance, discovery, ...
* Alerts		--> The configuration of our media types, scripts and actions
* Users			--> User configuration like user roles, user groups, authentication, API tokes, ...
* Administration	--> The administration part containing all global settings, housekeeper, proxies, queue, ...


Our last part the ```links``` part  contain a set of useful links that we can use:

* Support		--> This brings us to the technical support page that you can buy from Zabbix. Remember that your local partner is also able to sell these contracts and can help you in your own language. [Your local distributors](https://www.zabbix.com/distributors)
* Integrations		--> The official zabbix [integeration page](https://www.zabbix.com/integrations) 
* Help			--> The link to the documentation of your [Zabbix version](https://www.zabbix.com/documentation/7.0/)
* User settings		--> The user profile settings
* Sign out		--> Log out of the current session.

It's important to know that we have so far seen our dashboard with the Admin user and that this user is a ```Zabbix Super Admin``` user. This means that the user has no restrictions. Zabbix works with 3 different levels of users we have the regular users, Zabbix Admin and Zabbix Super Admin users. Let's have a look


![Image title](CH02/main-menu.png/)

* A ```Zabbix User``` will only see the red part of our ```main menu``` and will only be able to see our collected data.
* A ```Zabbix Admin``` will see the red part and the yellow part of the ```main menu``` and is able to change our configuration.
* A ```Zabbix Super Admin``` will see the complete ```main menu``` and so is able to change the configuration and all the global settings.



### Zabbix Users

Now that we know how the Zabbix dashboard is build up our first task will be to create a user.

### Zabbix hosts

### Host groups

### Items

### Interfaces

### Macros

## Data Flow

### Data Collection

### Simple Checks

## Zabbix Agent

### Zabbix agent Linux

### Zabbix agent windows

