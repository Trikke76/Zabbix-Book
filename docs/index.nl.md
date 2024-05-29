# Over wat gaat dit boek ?

Hallo, welkom en bedankt voor je interesse in mijn Zabbix-boek. Ik schreef het [Zabbix-cookbook](https://www.packtpub.com/product/zabbix-cookbook/9781784397586) en schreef samen met Richards [Zabbix 4 Network Monitoring](https://www.packtpub.com/product/ zabbix-4-network-monitoring-third-edition/9781789340266) een paar jaar geleden voor PackPub.

![Zabbix cookbook](images/book1.png){width=200} ![Zabbix Network Monitoring 4](images/book2.png){width=200}

Het cookbook, het eerste in zijn soort, is waarschijnlijk verouderd en zal worden vervangen door het [Zabbix 7 IT Infrastructure Monitoring Cookbook](https://www.packtpub.com/product/zabbix-7-it-infrastructure-monitoring-cookbook-third- edition/9781801078320), geschreven door Brian en Nathan, 2 mensen met wie ik graag samenwerk en die ik van harte kan aanbevelen.
Er zijn nog veel meer boeken verkrijgbaar bij Packt over Zabbix.
Een compleet overzicht vind je hier [Zabbix boeken bij pack](https://www.packtpub.com/search?query=zabbix).
Of als je graag niet-Engelse boeken wilt vinden, heeft Amazon een aantal boeken van Packt en andere uitgevers in het Chinees, Spaans en misschien ook enkele andere talen. [Andere boeken](https://www.amazon.com/s?k=zabbix&crid=3G0JRTVTKS7YU&sprefix=zabbix+%2Caps%2C167&ref=nb_sb_noss_2)

Omdat Zabbix een open source-product is en geld verdienen met de boeken nooit mijn bedoeling was, zette het mij aan het denken over hoe ik dingen anders kon doen.
Hoe je een nieuw boek kunt maken zonder gebruik te maken van een uitgever zoals ik eerder had gedaan.
Na een tijdje kwam ik op het idee om een boek te maken dat gratis zou zijn en dat zou worden bijgewerkt als er nieuwe versies uitkwamen.
Omdat ik een grote fan ben van documentatie in markdown of asciidoc, kwam ik op het idee om het boek in git te delen en markdown te gebruiken.
Het enige probleem dat nog overbleef, was hoe je die markdown-bestanden op een eenvoudige manier leesbaar kunt maken, zoals een boek? Na wat zoeken op zoek naar een goede oplossing vond ik [MkDocs](https://www.mkdocs.org). MkDocs is een Python-Markdown-bibliotheek die alles naar HTML kan converteren en waarvan een sjabloon kan worden gemaakt. Dus het probleem was opgelost en een nieuw boek was geboren.

## Wie ben ik ?

Mijn naam is Patrik Uytterhoeven en ik werk voor een Belgisch bedrijf genaamd Open-Future. Ik ben in januari 2013 bij dit bedrijf begonnen en dat was het
toen mijn reis ook met Zabbix begon. Ze gaven mij de kans om mijn ervaring op te bouwen en om gecertificeerd te worden als Zabbix-trainer.
Sinds dit jaar ben ik officieel 10j Zabbix trainer. Als je een van mijn trainingen wilt volgen, schrijf je dan gerust in voor een training op onze website [www.open-future.be](https://www.open-future.be). Waarom zou je een training volgen als je dit boek gratis kunt lezen, denk je nu? Omdat trainingen net als het boek je alle details uitleggen over hoe je dingen moet opzetten en doen, maar je ook waardevolle tips en feedback geven die je nooit uit een boek krijgt. Boeken kunnen nu eenmaal niet alles omvatten.

## Welk OS heb ik nodig ?

Omdat ik voornamelijk met op RHEL gebaseerde systemen werk en omdat ik ervan overtuigd ben dat RHEL de betere keuze is in productieomgevingen, heb ik ervoor gekozen om me te concentreren op het gebruik van een van de gratis beschikbare forks. Zabbix wordt ondersteund op Ubuntu, Debian, Suse, Raspberry ... en het kan worden gecompileerd op elk besturingssysteem dat op Unix is ​​gebaseerd, dus het is bijna onmogelijk om ze allemaal te behandelen. Het boek is echter Opensource en in GIT, dus voel je vrij om de code voor je favoriete smaak bij te dragen :). Ik zal [Rocky Linux](https://rockylinux.org/) 9 gebruiken in dit boek, maar het zou ook voor de meeste andere installaties moeten werken.

## Welke versie van Zabbix wordt in dit boek gebruikt ?

Omdat we bijna bij de release van Zabbix 7 zijn, zal ik me concentreren op versie 7, aangezien dit de nieuwe LTS zal zijn. Het zou ook moeten gelden voor de meeste andere versies, maar er zullen uiteraard kleine wijzigingen zijn. Als er in de toekomst voldoende steun is vanuit de gemeenschap om dit boek samen bij te werken, zou het geweldig zijn als we voor elke beschikbare LTS-versie een boek zouden kunnen maken.

## Hoe gebruik je dit boek?

In dit boek zal ik proberen alle onderwerpen te behandelen, laat het me gerust weten als er iets ontbreekt of doe gerust een pull-verzoek.
Het is niet nodig om vanaf pagina 1 te beginnen en het boek tot het einde te lezen. Sommige mensen zijn op zoek naar basiskennis, anderen willen misschien overgaan naar het leuke gedeelte, dus ik wil dat het boek voor iedereen nuttig is. Daarom zal ik proberen bij elk onderwerp zo goed mogelijk uit te leggen welke stappen precies nodig zijn om te reproduceren.

Er zullen momenten in het boek zijn waarop je wat code moet typen. Ik zal de commando's die je moet typen in een vak laten zien, net als hier.

```
# een commando
```

Aantekeningen bij enkele nuttige documentatie worden onderaan de pagina toegevoegd.


Hier is een eenvoudige voetnoot[^1]. Met wat extra tekst erna.

[^1]: Mijn referentie.


Als er belangrijke informatie is om te delen, zal ik aantekeningen toevoegen in de documentatie, zoals hier te zien is:

???+ note
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa, nec semper lorem quam in massa.

???+ info
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa, nec semper lorem quam in massa.

???+ tip
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa, nec semper lorem quam in massa.

???+ question
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa, nec semper lorem quam in massa.

???+ warning
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa, nec semper lorem quam in massa.

???+ bug
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa, nec semper lorem quam in massa.

???+ example
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa, nec semper lorem quam in massa.


