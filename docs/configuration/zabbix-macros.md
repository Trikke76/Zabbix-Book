# Macros

Macros are one of the concepts you need to understand in Zabbix. There are different types, and they make Zabbix more scalable and less static.

## [Built-in macros](https://www.zabbix.com/documentation/7.0/en/manual/appendix/macros/supported_by_location)

They correspond to Zabbix internal macros, they are represented using this notation `{MACRO}`, the complete list is available [here](https://www.zabbix.com/documentation/7.0/en/manual/appendix/macros/supported_by_location). Each built-in macro can be used in one or more cases.

For example, the `{HOST.HOST}` built-in macro can be used to retrieve the host in a trigger expression.
or the macro `{HOST.IP}` which does the same thing but retrieves the IP of the host.

When you wa

## [Function macros](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions)

You may want to manipulate the value of a macro, to shorten or extract a part for example.
The syntax of a macro function is as follows: `{macro.func(params)}`

|Function|Description|
|--|--|
|[fmtnum](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions#fmtnum)|Number formatting to control the number of digits printed after the decimal point.|
|[fmttime](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions#fmttime)|Time formatting.|
|[iregsub](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions#iregsub)|Substring extraction by a regular expression match (case-insensitive).|
|[regsub](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions#regsub)|Substring extraction by a regular expression match (case-sensitive).|

## [User macros](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/user_macros)

Macros are what give Zabbix its great flexibility. They can be defined globally, or at template or host level.
They are always preceded by a **\$** and are noted thus: `{$MACRO}`.

It is important to note that Zabbix prioritises user macros like this:

1- host level macros (checked first)
2- macros defined for first level templates of the host (i.e., templates linked directly to the host), sorted by template ID
3- macros defined for second level templates of the host, sorted by template ID
4- macros defined for third level templates of the host, sorted by template ID, etc.
5- global macros (checked last)

These macros can be used for many [endrois](https://www.zabbix.com/documentation/7.0/en/manual/appendix/macros/supported_by_location_user), they are commonly used to define a threshold and serve as a default value in a template that can be overloaded with host-level macros.

In order to have templates that are reusable and functional in a majority of situations, users macros must be abused.

## [Low-level discovery macros](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/lld_macros)

This type of macro is a little special, it is used in the case of [Low-level discovery](https://www.zabbix.com/documentation/7.0/en/manual/discovery/low_level_discovery) and is preceded by a **#** `{#MACRO}`, it is useful for variabbilising the creation of a prototype item, prototype trigger or prototype host.

## [Expression macro](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/expression_macros)

Ces macros sont très spécifiques, elles s'utilisent exclusivement dans les graphs name et dans les maps, elles utilisent la syntaxe suivantes: `{?EXPRESSION}`.
`{?avg(/{HOST.HOST}/{ITEM.KEY},1h)}`

## [User macro with context](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/user_macros_context)

There's one last subject concerning macros, and it's not the least important: very soon after using your first user macros to define thresholds, you'll realise that you want to
have a different threshold depending on ... the context, they use the syntax: `{$MACRO: "static text"}`
You want to set the global threshold to 80%, but on a particular partition you want to increase the threshold to 90%. To do this, we'll use the context.

_Example:_
{$LOW_SPACE_LIMIT}: 80
{$LOW_SPACE_LIMIT:/tmp}:90

In this example, all the mount points on our server will have a default threshold of 80% except for /tmp, which will have a custom threshold of 90%.
