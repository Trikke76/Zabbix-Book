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
| Time zone | Select the time zone per user or use the default timezone that is configured on the Zabbix server. |
| Theme	    | Here users can select their own look and feel by choosing one of the 4 themes provided by Zabbix or another custom made theme. Default will switch to the default theme chosen by the admin. |
| Auto-login | Check this box so that the user will be remembered for 30 days. The browser must accept cookies for this to work. |
| Auto-logout | Checking this box makes sure the user gets logged out automatically, after the set amount of seconds (minimum 90 seconds, maximum 1 day). |
|           | Time suffixes are supported, e.g. 90s, 5m, 2h, 1d. |
|           | Note that this option will not work if : |
|           | * If the "Show warning if Zabbix server is down" global configuration option is enabled and Zabbix frontend is kept open. |
|           | * When Monitoring menu pages perform background information refreshes. |
|           | * If logging in with the Remember me for 30 days option checked. |
| Refresh   | Set the refresh rate used for graphs, plain text data, etc. Can be set to 0 to disable. Time suffixes are supported. Ex:  90s, 5m, 1h. |
| Rows per page	| Define how many rows per page will be displayed in lists. |
| URL (after login) | You can make Zabbix transfer the user to a specific URL after successful login. This can be useful for monitors in NOC team for example so you arrive on a specific dashboard that is maximised. |
|           | The URL can be the full url or only the relative url like : zabbix.php?action=host.list |

### User Media

The tab ''' Media ''' contains a list of all media that is defined for out user. Media is used for sending notifications to the user. We can click the ''' Add ''' button.

![User Media](image/user-media.png)

Adding the media here is not enough to receive notification we also need to configure our media properly and we still need to configure Actions as well.
When pressing the ''' Add ''' button we get a popup where we can select some information.

![User Media popup](image/user-media-popup.png)

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
<th>Type</th><td>A drop down list with the names of all media types. When a media type is disabled it will be in red.</td>
</tr>
<tr>
<th>Send to</th><td>Here we can provide contact information. For an email media type it is possible to add several addresses by clicking on '''Add'''  below the address field. In this case, the notification will be sent to all email addresses provided. It's also possible to specify recipient name in the Send to field of the email recipient in a format 'Recipient name &lt;address1@company.com>'. Note that if a recipient name is provided, an email address should be wrapped in angle brackets (<>). UTF-8 characters in the name are supported, quoted pairs and comments are not. For example: John Doe &lt;manager@open-future.com> and manager@nycdatacenter.com are both valid formats. Incorrect examples: John Doe manager@open-future.com, %%"Zabbix\@\&lt;H(comment)Q\>" zabbix@company.com %%.<td>
</tr>
<tr>
<th>when active</th><td>The time when media will be active from monday till sundat, 1-7 and the time from 00:00 till 24:00 for example only in weekends from 6 in the morning till 5 in the evening: 6-7,06-17:00i. This is based on the user his timezone</td>
</tr>
<tr>
<th>Use if severity</th><td>A list of checkboxes from the severities you would like to recieve notifications from. Selected severities will be displayed in color. !! Read the warning below!!</td>
</tr>
<tr>
<th>Status</th><td>Status of the media we have selected either enabled or disabled ( in use or not )</td>
</tr>
</tbody>
</table>
</body>
</html>

???+ warning
    When selecting the different severities be aware that you have to select '''Not classified''' if you want to receive notifications about non trigger events like internal events. For more info check out : [Event sources](https://www.zabbix.com/documentation/7.0/en/manual/config/events/sources). This is something that is not obvious and Zabbix documentation could be better in explaining this imho.
