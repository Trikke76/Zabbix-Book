# Host groups

We have seen that ```Host groups``` can be created directly when we create a new ```Zabbix host```. Another way to create them is by a ```Super Admin``` going to Data collection -> Host groups. Next press the button ```Create host group``` in the upper right corner of the screen.

![host-groups](image/host-groups.png)

As you can see we are using forward slashes in our hostname. When you make use of nested group you can use the '/' to separate groups. 

???+ Note
    When creating nested groups, Parent groups don't have to exist. So we can have only the group ```open-future``` without any of the parent groups. It's up to the user to create them or not. Also group names cannot have `/` in their names. We cannot escape the `/` character. Also leading and trailing slashes and multiple slashes in a row are not allowed.


