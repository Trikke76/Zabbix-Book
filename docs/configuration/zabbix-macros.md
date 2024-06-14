# Macros

Macros are one of the concepts you need to understand in Zabbix. There are different types, and they make Zabbix more scalable and less static.
In this topic we will explain the different types of macros and how to use them.

We will cover:

- Built-in macros
- Function macros
- User macros
- Low-level discovery macros
- Expression macro
- User macro with context

---

## Built-in macros

They correspond to Zabbix internal macros, they are represented using this notation `{MACRO}`, the complete list is available [here](https://www.zabbix.com/documentation/7.0/en/manual/appendix/macros/supported_by_location). Each built-in macro can be used in one or more cases.

For example, the `{HOST.HOST}` built-in macro can be used to retrieve the host in a trigger expression.
or the macro `{HOST.IP}` which does the same thing but retrieves the IP of the host.

---

## Function macros

You may want to manipulate the value of a macro, to shorten or extract a part for example.
The syntax of a macro function is as follows: `{macro.func(params)}`

|Function|Description|
|--|--|
|[fmtnum](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions#fmtnum)|Number formatting to control the number of digits printed after the decimal point.|
|[fmttime](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions#fmttime)|Time formatting.|
|[iregsub](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions#iregsub)|Substring extraction by a regular expression match (case-insensitive).|
|[regsub](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions#regsub)|Substring extraction by a regular expression match (case-sensitive).|


---

## User macros

Macros are what give Zabbix its great flexibility. They can be defined globally, or at template or host level.
They are always preceded by a **\$** and are noted thus: `{$MACRO}`.

It is important to note that Zabbix prioritises user macros like this:

1. host level macros (checked first)
2. macros defined on first level templates of the host (i.e., templates linked directly to the host), sorted by template ID
3. macros defined on the second level templates of the host, sorted by template ID
4. macros defined on the third level templates of the host, sorted by template ID, etc.
5. global macros (checked last)

These macros can be used in  many [locations](https://www.zabbix.com/documentation/7.0/en/manual/appendix/macros/supported_by_location_user),
they are commonly used to define a threshold and serve as a default value in a template that can be overloaded with host-level macros.

In order to have templates that are reusable and functional in the majority of the situations, users macros must be used.
It's also adviced to not use global macros when not needed as they are not portable when exporting hosts or templates.

So when we define ```global macros``` they will be overwritten by ```template macros``` and our template macros will be overwritten by ```host macros```.

![macro order](image/macros/macros-order.png)

It's easy so overwrite a macro by accident to solve this problem Zabbix has the option on ```Host``` and on ```Template``` level to see if there are any macros defined yet with the same name.
This makes it easy to see if you have this macro defined already and if it will be overwritten.

![macro inheritance](image/macros/inherited-macros.png)

In this screenshot we can see that there is a ```Global macro``` defined with a value of 100 and that we have the the same macro on Template level that overrides the value of the ```Global macro```.
However the effective vale is coming from our ```Host macro```.


---

## Low-level discovery macros

This type of macro is a little special, it is used in the case of [Low-level discovery](https://www.zabbix.com/documentation/7.0/en/manual/discovery/low_level_discovery) and is preceded by a **#** `{#MACRO}`, it is useful for variabilize the creation of a prototype item, prototype trigger or prototype host.


---

## Expression macro

These macros are very specific, they are used exclusively in name graphs and in maps, they use the following syntax: `{?EXPRESSION}`.
`{?avg(/{HOST.HOST}/{ITEM.KEY},1h)}`


---

## User macro with context

There's one last subject concerning macros, and it's not the least important: very soon after using your first user macros to define thresholds, you'll realise that you want to
have a different threshold depending on ... the context, they use the syntax: `{$MACRO: "static text"}`
You want to set the global threshold to 80%, but on a particular partition you want to increase the threshold to 90%. To do this, we'll use the context.


---

### Example:

- {$LOW_SPACE_LIMIT}: 80
- {$LOW_SPACE_LIMIT:/tmp}:90

In this example, all the mount points on our server will have a default threshold of 80% except for /tmp, which will have a custom threshold of 90%.

---

## Some useful links

- [https://www.zabbix.com/documentation/7.0/en/manual/appendix/macros/supported_by_location](https://www.zabbix.com/documentation/7.0/en/manual/appendix/macros/supported_by_location)
- [https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/macro_functions)
- [https://www.zabbix.com/documentation/7.0/en/manual/config/macros/user_macros](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/user_macros)
- [https://www.zabbix.com/documentation/7.0/en/manual/config/macros/lld_macros](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/lld_macros)
- [https://www.zabbix.com/documentation/7.0/en/manual/config/macros/expression_macros](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/expression_macros)
- [https://www.zabbix.com/documentation/7.0/en/manual/config/macros/user_macros_context](https://www.zabbix.com/documentation/7.0/en/manual/config/macros/user_macros_context)
