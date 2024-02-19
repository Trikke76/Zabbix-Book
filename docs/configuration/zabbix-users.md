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

## Zabbix User types

Before we create new users it's important to know that Zabbix has 3 user types that are built-in.

- Zabbix user -> This is a normal user that only has read only permissions if given. So there are no permissions assigned by default.
- Zabbix admin -> A user with read/write permissions. Just like the Zabbix user there are not permissions by default
- Zabbix super admin -> A user with read/write permissions that can't be revoked

Besides these differences these users also have different accesses rights in our menu. Let's have a closer look.

- A normal user wil only see a part of our menu on the left. Also some sub sections of the menu will not be visible. An ```Admin``` user will have more rights than a regular user and will be able to make some configuration changes in Zabbix. A ```Super Admin``` user will have unlimted right and see every part of the menu. The only way to limit a ```Super Admin``` will be by making use of roles. Something we cover later.
- An ```Admin``` user will have more rights than a regular user and will be able to make some configuration changes in Zabbix.
- A ```Super Admin``` can access all parts of the menu. The user will have read/write access to all host and template groups. Access can't be revoked by denying access to groups like with a normal admin.

This table gives an overview of all the permissions a Zabbix user, admin and super admin have:

<html>
<head>
<style>
thead th { text-align:left; background:grey; color:white; height: 20px }
tbody th { text-align:center; background: lightgrey; color:whitwhitee }
</style>
</head>
<body>
<table>
  <thead>
    <tr>
      <th> </th><th>Zabbix User</th><th>Zabbix Admin</th><th>Zabbix Super Admin</th>
    </tr>
  </thead>
<tbody>
  <thead>
    <tr>
      <th >Dashboards</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    <tr>
  </thead>
  <thead>
    <tr>
      <th>Monitoring</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
  </thead>
    <tr>
      <th>- Problems</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Hosts</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Latest data</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Maps</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Discovery</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
  <thead>
    <tr>
      <th>Services</th><th><center>&#9989;</center></th><th><center>&#9989;</center></th><th><center>&#9989;</center></th>
    </tr>
  </thead>
    <tr>
      <th>- Services</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- SLA</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- SLA Report</th><td><center>&#9989;;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
  <thead>
    <tr>
      <th>Inventory</th><th><center>&#9989;</center></th><th><center>&#9989;</center></th><th><center>&#9989;</center></th>
    </tr>
  </thead>
    <tr>
      <th>- Overview</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Hosts</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
  <thead>
    <tr>
      <th>Reports</th><th><center>&#9989;</center></th><th><center>&#9989;</center></th><th><center>&#9989;</center></th>
    </tr>
  </thead>
    <tr>
      <th>- System information</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Scheduled reports</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Availability report</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Triggers top 100</th><td><center>&#9989;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Audit log</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Action log</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Notifications</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
  <thead>
    <tr>
      <th>Data Collection</th><th><center>&#10060;</center></th><th><center>&#9989;</center></th><th><center>&#9989;</center></th>
    </tr>
  </thead>
    <tr>
      <th>- Template groups</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Host groups</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Templates</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Hosts</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Maintenance</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Event correlation</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Discovery</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
  <thead>
    <tr>
      <th>Alerts</th><th><center>&#10060;</center></th><th><center>&#9989;</center></th><th><center>&#9989;</center></th>
    </tr>
  </thead>
    <tr>
      <th>- Trigger actions</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Service actions</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Autoregistration actions</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Internal actions</th><td><center>&#10060;</center></td><td><center>&#9989;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Media types</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Scripts</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
  <thead>
    <tr>
      <th>Users</th><th><center>&#10060;</center></th><th><center>&#10060;</center></th><th><center>&#9989;</center></th>
    </tr>
  </thead>
    <tr>
      <th>- User groups</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- User roles</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Users</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Api tokens</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Authentication</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
  <thead>
    <tr>
      <th>Administration</th><th><center>&#10060;</center></th><th><center>&#10060;</center></th><th><center>&#9989;</center></th>
    </tr>
  </thead>
    <tr>
      <th>- General</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Audit log</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Housekeeping</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Proxies</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Macros</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
    <tr>
      <th>- Queue</th><td><center>&#10060;</center></td><td><center>&#10060;</center></td><td><center>&#9989;</center></td>
    </tr>
  </tbody>
</table>
</body>
</html>

- An ```Admin``` user will have more rights than a regular user and will be able to make some configuration changes in Zabbix.
- A ```Super Admin``` can access all parts of the menu. The user will have read/write access to all host and template groups. Access can't be revoked by denying access to groups like with a normal admin.

## User Roles

User roles have been in Zabbix since version 5.2 and make our live more easy by allowing us to make some custom adjustments to the standard defind user types in Zabbix. 

???+ warning
    Be aware the no permissions can be added to user roles only permissions can be revoked.

## Creating a new User in Zabbix

So now that we are in the users section of Zabbix it's probably a good time to create a new user for our system. If you skipped previous step go to the menu '''Users''' -> '''Users'''.

Click on the top right on '''Create user''' and fill in the details of your new users. You will see that some fiels have red asterisks in front of them like Username and Password, ... this means that those feelds are mandatory to fill in.

![new user form](image/new-user-form.png)

#### Strengthen the Zabbix password policy

Zabbix passwords rely on a minimum length of 8 characters and also block a list of easy to guess passwords. We can make our passwords more secure by telling Zabbix that our passwords must contain uppercase and lowercase characters, a digit and a special character. This policy is a global policy that will be enforced and we have to set this policy as Super Admin. Go to the menu Users -> Authentication in older versions you can find it under Administration Authentication.



![User Password policy](image/user-policy.png)


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
    <th>Username</th><td>A unique name that will be used as username when we login.</td>
  </tr>
  <tr>
    <th>Name</th><td>The users firstname this field is optional visible in acknowledgment information and notification recipient information if set.</td>
  </tr>
  <tr>
    <th>Last Name</th><td>Users last name. Optional, this field is optional visible in acknowledgment information and notification recipient information if set.</td>
  </tr>
  <tr>
    <th>Groups</th><td>Select what group the user will belong to. Atleast 1 group needs to be selected. This feeld will auto complete or you can press the '''Select''' button at the end of the field.</td>
  </tr>
  <tr>
    <th>Password</th><td>There are 2 password fields they can only be used for internal authentication but more about this later. If the user has the Super admin role then clicking on the Change password button opens an additional field to entering the current (old) password. On a successful password change, the user for which the password was changed will be logged out of all active sessions.</td>
  </tr>
  <tr>
    <th>Language</th><td>Language of the frontend. The php gettext extension is required for the translations to work. And the language needs to be configured on the system. See the chapter "Installing Zabbix" in case you forgot.</td>
  </tr>
  <tr>
    <th>Timezone</th><td>Select the time zone per user or use the default timezone that is configured on the Zabbix server.</td>
  </tr>
  <tr>
    <th>Theme</th><td>Here users can select their own look and feel by choosing one of the 4 themes provided by Zabbix or another custom made theme. Default will switch to the default theme chosen by the admin.</td>
  </tr>
  <tr>
    <th>Auto-Login</th><td>Check this box so that the user will be remembered for 30 days. The browser must accept cookies for this to work.</td>
  </tr>
  <tr>
    <th>Auto-Logout</th><td>Checking this box makes sure the user gets logged out automatically, after the set amount of seconds (minimum 90 seconds, maximum 1 day). Time suffixes are supported, e.g. 90s, 5m, 2h, 1d. Note that this option will not work if : <li>If the "Show warning if Zabbix server is down" global configuration option is enabled and Zabbix frontend is kept open.</li><li>When Monitoring menu pages perform background information refreshes.</li><li>If logging in with the Remember me for 30 days option checked.</li></td>
  </tr>
  <tr>
    <th>Refresh</th><td>Set the refresh rate used for graphs, plain text data, etc. Can be set to 0 to disable. Time suffixes are supported. Ex: 90s, 5m, 1h.</td>
  </tr>
  <tr>
    <th>Rows per page</th><td>Define how many rows per page will be displayed in lists.</td>
  </tr>
  <tr>
    <th>URL(after login)</th><td>You can make Zabbix transfer the user to a specific URL after successful login. This can be useful for monitors in NOC team for example so you arrive on a specific dashboard that is maximised. You can make Zabbix transfer the user to a specific URL after successful login. This can be useful for monitors in NOC team for example so you arrive on a specific dashboard that is maximised.</td>
  </tr>
</tbody>
</table>
</body>
</html>


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
