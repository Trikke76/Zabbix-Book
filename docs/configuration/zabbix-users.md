# Zabbix Users

Now that we know how the Zabbix dashboard is build up our first task will be to create a user. In case you missed it the standard Zabbix (yes the capital Z here is eeded to login.) user is Admin and has the password zabbix so we need to change this ASAP. The most confusing part is probably that the user Admin in zabbix is actually a 'super admin' but more about that later.

## Changing the Zabbix super admin password

In our menu on the right side of the screen click the 'Users' section and then choose '''users'''. As you see here in the screenshot.

![user menu](image/user-menu.png)

You will now see a list of all the users that are created on the system when installing a new Zabbix instance. Here you will always see a list of all users that are configured on the system.

![user list](image/users-list.png)

To change the password do the following steps
- Click user '''Admin'''
- Click on the button '''Change password'''
- Fill in the current password '''zabbix'''
- Fill in the new password twice and press '''Update''' at the bottom of the page.


## Creating a new User in Zabbix

So now that we are in the users section of Zabbix it's probably a good time to create a new user for our system. If you skipped previous step go to the menu '''Users''' -> '''Users'''.

Click on the top right on '''Create user''' and fill in the details of your new users. You will see that some fiels have red asterisks in front of them like Username and Password, ... this means that those feelds are mandatory to fill in.

![new user form](image/new-user-form.png)

| Parameter | Description   |
| :----     | :----          |
| Username  | A unique name that will be used as username when we login|
| Name      | The users firstname this field is optional visible in acknowledgment information and notification recipient information if set.|
| Last name | Users last name. Optional, this field is optional visible in acknowledgment information and notification recipient information if set.|
| Groups    | Select what group the user will belong to. Atleast 1 group needs to be selected. This feeld will auto complete or you can press the '''Select''' button at the end of the field. |
| Passowrd  | There are 2 password fields they can only be used for internal authentication but more about this later. If the user has the Super admin role then clicking on the Change password button opens an additional field to entering the current (old) password. On a successful password change, the user for which the password was changed will be logged out of all active sessions.|
| Language  | Language of the frontend. The php gettext extension is required for the translations to work. And the language needs to be configured on the system. See the chapter "Installing Zabbix" in case you forgot. |


Time zone	Select the time zone to override global time zone on user level or select System default to use global time zone settings.

Theme	Defines how the frontend looks like:
System default - use default system settings
Blue - standard blue theme
Dark - alternative dark theme
High-contrast light - light theme with high contrast
High-contrast dark - dark theme with high contrast

Auto-login	Mark this checkbox to make Zabbix remember the user and log the user in automatically for 30 days. Browser cookies are used for this.

Auto-logout	With this checkbox marked the user will be logged out automatically, after the set amount of seconds (minimum 90 seconds, maximum 1 day).
Time suffixes are supported, e.g. 90s, 5m, 2h, 1d.
Note that this option will not work:
* If the "Show warning if Zabbix server is down" global configuration option is enabled and Zabbix frontend is kept open.
* When Monitoring menu pages perform background information refreshes.
* If logging in with the Remember me for 30 days option checked.

Refresh	Set the refresh rate used for graphs, plain text data, etc. Can be set to 0 to disable.
Time suffixes are supported, e.g. 90s, 5m, 1h.

Rows per page	You can determine how many rows per page will be displayed in lists.

URL (after login)	You can make Zabbix transfer the user to a specific URL after successful login, for example, to Problems page.
