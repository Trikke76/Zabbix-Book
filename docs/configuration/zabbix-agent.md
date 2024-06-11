# Zabbix Agent

In this topic we will talk about the Zabbix agent. The Zabbix agent is a native agent made by Zabbix that we can deploy on different Operating Systems and that supports a wide range of build-in items keys.
Zabbix has 2 agents we have the ```Zabbix agent``` and we have the ```Zabbix agent 2``` So when you read in this book about the ```Zabbix agent``` we talk about the 1st agent. This can sound a bit
weird having 2 agents but it's not. The agent was developed years ago when zabbix was first released and is written in C the agent 2 is written in GO and is developed next to the Agent 1.
We will see later what the advantages and disadvantages are of both and give you tips to choose the agent that fits best for you.
To make things more complex both agents can be configure to work in ```active``` or in ```passive``` mode or in both.


So in this topic we will explain you all the differences :

- Zabbix Agent1
- Zabbix Agent2
- Agent1 vs Agent2
- Active Agent
- Passive Agent
- Active vs Passive
- ZZabbix agent windows


## Zabbix Agent 1

Our Zabbix agent or the legacy agent can run on a whole set of supported platforms. At the time of writing the agent on Zabbix 7 supports:

- Linux (Redhat, Suse, Ubuntu, Debian, Rocky, Alma, ...)
- FreeBSD
- Windows
- MacOS
- Raspberry Pi
- HPUnix
- OpenBSD
- AIX
- Solaris

???+ info
    A full up to date list can be found here https://www.zabbix.com/download

The agent can be installed on a host to collect data from that host or from an application running on that host.
The advantage is that the agent has a very small footprint and that we can extend the agent by creating our own scripts.
Agents can work in active or in passive mode or can do this in both ways simultaniously.












## Zabbix Agent 2

## Agent1 vs Agent2

| Parameter 			| Agent					 | Agent 2 				|
| :----				| :----					 |:----					|
| Programming language 		| C					 | Go and some parts in C		|
| Linux daemonization 		| Yes					 | By systemd only			|
| Run as Windows service	| Yes					 | Yes					|
| Supported extensions		| Loadable Modules in C	 		 | Plugins in Go			|
| Supported platforms		| All					 | Linux, Window, any OS with GO	|
| Concurrency			| Active checks are executed sequentialy | All check executed concurrently	|
| Scheduled/flexible intervals	| Passive check only			 | Acive and Passsive checks		|
| Persistent storage 		| No					 | Yes (SQLite3)			|
| Timeout settings		| On agent level only			 | Plugins can override the timout	|
| Changes user at runtime	| Yes (on Unix like sytems only)	 | No (controlled by SystemD		|
| Cipersuits user configurable  | Yes					 | No					|

???+ info
    For more details bout the differences check the official documentation https://www.zabbix.com/documentation/6.0/en/manual/appendix/agent_comparison
      


)
We can detect the generation from the agent by using the ```agent.variant``` item key this will return us if it is agent generation 1 or 2


## Active Agent

## Passive Agent

## Active vs Passive 


## Zabbix agent windows
