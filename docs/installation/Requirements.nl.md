# Vereisten

Zabbix heeft een reeks vereisten waaraan moet worden voldaan op hardwareniveau en softwareniveau. Deze vereisten kunnen in de loop van de tijd veranderen en zijn ook afhankelijk van de grootte van uw installatie en de software die u kiest.
Dus voordat je metal gaat kopen of een willekeurige databaseversie installeert, bekijk de Zabbix-documentatie en controleer de nieuwste vereisten voor de versie die je wilt installeren. De nieuwste vereisten zijn [hier](https://www.zabbix.com/documentation/current/en/manual/installation/requirements) te vinden. Vergeet niet uw juiste *Zabbix*-versie uit de lijst te selecteren.

Als je niet van plan bent iets groots uit te voeren, alleen een kleine installatie of een testopstelling, zal Zabbix gelukkig werken op een systeem met 2cpu en 8G ram. Maar het hangt allemaal af van hoe groot je setup zal zijn en hoeveel items je gaat monitoren, triggers die je gaat creëren en hoe lang je die gegevens wilt bewaren. Mijn advies in de tijd van virtualisatie is dat je klein kunt beginnen en later meer kunt toevoegen.

Voor de installatie kunt u ervoor kiezen om alle componenten op 1 server te installeren of elk component op een andere server. Maak voor het gebruiksgemak een paar aantekeningen voor uzelf:

| server                | ip    |
|:----                  | :---- |
| zabbix server         |       |
| database server       |       |
| web server            |       |


???+ tip
    Terwijl zabbix streepjes "-" in zijn namen gebruikt wanneer we pakketten zoals zabbix-get of zabbix-sender moeten installeren, gebruiken de binaire bestanden "_". zoals zabbix_sender of zabbix_server. Dit kan natuurlijk variëren, afhankelijk van of u de pakketten uit de originele Zabbix-repository's gebruikt of niet. Houd er rekening mee dat het soms nogal verwarrend kan zijn en dat als u een pakket met een streepje installeert, het binaire bestand misschien met een onderstrepingsteken is.

## Basisconfiguratie van het OS

### Firewall

Het is belangrijk dat onze Zabbix-server een besturingssysteem heeft dat goed is voorbereid voordat we onze monitoringtool gaan installeren. Eerst moeten we ervoor zorgen dat onze firewall is geïnstalleerd.

```# dnf install firewalld --now```

Onze firewall is nu geïnstalleerd en we zijn klaar om de benodigde poorten te configureren. Voor onze Zabbix-server moeten we toegang tot poort 10051/tcp toestaan. Dit is de poort waar onze Zabbix-trapper naar binnenkomende gegevens luistert. We moeten deze poort dus in onze firewall openen om toegang tot onze Zabbix-trapper mogelijk te maken.

```# firewall-cmd --add-service=Zabbix-server --permanent```

of als de service niet bekend is

```# firewall-cmd --add-port=10051/tcp --permanent```

/// note | firewalld
"Firewalld is de vervanging van iptables in Redhat en stelt ons in staat wijzigingen onmiddellijk beschikbaar te maken zonder dat u een service opnieuw hoeft te starten. Het is mogelijk dat uw distributie in dit geval geen gebruik maakt van [Firewalld](https://www.firewalld.org) je moet naar de documentatie van je besturingssysteem kijken."
///

### Tijdserver

Een ander ding dat we moeten configureren is het instellen van de tijdserver en het synchroniseren van onze Zabbix-server met de tijdserver door gebruik te maken van een ntp-client. Dit moet worden gedaan voor de Zabbix-server, maar ook voor de apparaten die we zullen monitoren, aangezien tijd erg belangrijk is voor Zabbix. Stel je voor dat een van onze hosts een tijdzone heeft die verkeerd is. We zouden uiteindelijk op zoek kunnen gaan naar een probleem in Zabbix dat 6 uur geleden gebeurde, terwijl het misschien pas 2 uur geleden was gebeurd.

```# dnf install chronyd --now```

Chrony zou nu geïnstalleerd moeten zijn, ingeschakeld en actief moeten zijn. Dit kan worden geverifieerd met het commando:

```# systemctl status chronyd```

/// note | dnf
"dnf is een pakketmanager van RedHat, je moet dnf vervangen door de juiste pakketmanager zoals zyper, apt, yum, ... chrony is een vervanging voor ntpd en doet het beter omdat het sneller en nauwkeuriger is. Als je besturingssysteem [ chrony](https://chrony-project.org/) dan is ntpd misschien nog steeds beschikbaar."
///

Zodra Chrony is geïnstalleerd, moeten we ook onze juiste tijdzone instellen. We kunnen eerst met 'timedatectl' kijken hoe onze tijd is geconfigureerd

```
# timedatectl
               Local time: Thu 2023-11-16 15:09:14 UTC
           Universal time: Thu 2023-11-16 15:09:14 UTC
                 RTC time: Thu 2023-11-16 15:09:15
                Time zone: UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

Zorg ervoor dat de service chronyd actief is, zie hierboven hoe u dit kunt doen als u deze gemist heeft. We kunnen de juiste tijdzone kiezen uit een lijst die we kunnen opzoeken met de volgende opdracht:

```
# timedatectl list-time zones
```

Dit geeft ons een lijst met alle beschikbare tijdzones. Kies degene die het dichtst bij u in de buurt is.

```
Africa/Abidjan
Africa/Accra

...

Pacific/Tongatapu
Pacific/Wake
Pacific/Wallis
UTC
```

We kunnen nu onze juiste tijdzone configureren met de volgende opdracht:

```
timedatectl set-time zone Europe/Brussels
```

Als we opnieuw kijken, zouden we onze tijdzone correct geconfigureerd moeten zien.

```
# timedatectl
               Local time: Thu 2023-11-16 16:13:35 CET
           Universal time: Thu 2023-11-16 15:13:35 UTC
                 RTC time: Thu 2023-11-16 15:13:36
                Time zone: Europe/Brussels (CET, +0100)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

???+ note
    "Sommige mensen willen graag alle servers in de UTC-tijdzone installeren, zodat alle serverlogboeken zich in dezelfde tijdzone bevinden als ze servers over de hele wereld hebben. Zabbix ondersteunt op gebruikers gebaseerde tijdzone-instellingen, dus het is mogelijk om de tijdzone in UTC aan te houden de server en voeg vervolgens de juiste tijdzone toe in de gebruikersinterface als je wilt."

We kunnen testen of Chrony ook synchroniseert met de juiste tijdservers door de opdracht chronyc uit te voeren

```
# chronyc
chrony version 4.2
Copyright (C) 1997-2003, 2007, 2009-2021 Richard P. Curnow and others
chrony comes with ABSOLUTELY NO WARRANTY.  This is free software, and
you are welcome to redistribute it under certain conditions.  See the
GNU General Public License version 2 for details.

chronyc>
```

Vervolgens typen we ```sources```

```
chronyc> sources
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^- 51-15-20-83.rev.poneytel>     2   9   377   354   +429us[ +429us] +/-  342ms
^- 5.255.99.180                  2  10   377   620  +7424us[+7424us] +/-   37ms
^- hachi.paina.net               2  10   377   412   +445us[ +445us] +/-   39ms
^* leontp1.office.panq.nl        1  10   377   904  +6806ns[ +171us] +/- 2336us
```

Hier kunnen we zien dat we een aantal ntp-servers gebruiken die zich niet in ons eigen land bevinden, dus we kunnen beter overschakelen naar een aantal tijdservers in ons lokale land, of als we een tijdserver in ons bedrijf hebben, kunnen we deze gebruiken. We kunnen hier enkele lokale tijdservers vinden: [https://www.ntppool.org/](https://www.ntpool.org)

Om dit te veranderen moeten we ons configuratiebestand "/etc/chrony.conf" bewerken en de bestaande ntp-server vervangen door onze lokale server.

```
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
pool 2.centos.pool.ntp.org iburst
```

En verander het naar een lokale server:

```
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
pool be.pool.ntp.org iburst
```

Vergeet uiteraard niet de ntpd-client opnieuw op te starten.

```
# systemctl restart chronyd
```

Als we nog eens kijken, zien we dat we nu onze lokale tijdservers gebruiken.

```
chronyc> sources

MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^- ntp1.unix-solutions.be        2   6    17    43   -375us[ -676us] +/-   28ms
^* ntp.devrandom.be              2   6    17    43   -579us[ -880us] +/- 2877us
^+ time.cloudflare.com           3   6    17    43   +328us[  +27us] +/- 2620us
^+ time.cloudflare.com           3   6    17    43   +218us[  -83us] +/- 2815us
```
