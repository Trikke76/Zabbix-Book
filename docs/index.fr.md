# De quoi parle ce livre ?

Bonjour, bienvenue et merci de votre intérêt pour mon livre Zabbix. J'ai écrit le [livre de recettes Zabbix](https://www.packtpub.com/product/zabbix-cookbook/9781784397586) et co-écrit avec Richards [Zabbix 4 Network Monitoring](https://www.packtpub.com/product/ zabbix-4-network-monitoring-third-edition/9781789340266) il y a quelques années pour PackPub.

![Zabbix cookbook](images/book1.png){width=200} ![Zabbix Network Monitoring 4](images/book2.png){width=200}

The cookbook the first of it's kind probably outdated and will be replaced by the [Zabbix 7 IT Infrastructure Monitoring Cookbook](https://www.packtpub.com/product/zabbix-7-it-infrastructure-monitoring-cookbook-third-edition/9781801078320), written by Brian and Nathan, 2 people I like a lot to work with and can higly recommend. There are many more books available from Packt about Zabbix a complete overview can be found here [Zabbix books at pack](https://www.packtpub.com/search?query=zabbix). Or if you like to find some non English books Amazon has some books form Packt and other Publishers in Chinese, Spanish and maybe some other languages as well. [Other books](https://www.amazon.com/s?k=zabbix&crid=3G0JRTVTKS7YU&sprefix=zabbix+%2Caps%2C167&ref=nb_sb_noss_2)

As Zabbix is an opensource product and making money out of the books was never my intention, it got me thinking how to do things different.
How to make a new book without using a publisher like I had done before.
After a while, I came up with the idea to make a book that would be free and that would be updated when new versions came out.
Since I am a huge fan of documentation in markdown or asciidoc I came up with the idea to share the book in git and use markdown.
The only problem left was how to make those markdown files readable in an easy way like a book ? After some searching trying to look for a good solution I found [MkDocs](https://www.mkdocs.org). MkDocs is a Python-Markdown library that can convert everything to HTML and can be templated. So the problem was solved and a new book was born.


## Who am I ?

My name is Patrik Uytterhoeven and I work for a Belgium company named Open-Future. I started at this company at Januari 2013 and that's
when my journey started with Zabbix as well. They gave me the opportunity to build my experience and to get certified as Zabbix trainer.
Since this year I am officially 10y Zabbix trainer. If you would like to follow one of my trainings feel free to register for a training at our website [www.open-future.be](https://www.open-future.be). Why would you follow a training if you can read this book for free are you now thinking? Because trainings just like the book explain you all the details on how to set up and do things but also give you valueable tips and feedback that you never get from a book. Books just can't cover everything.


## What OS do I need ?

Since I work mostly with RHEL based systems and since I am convinced that RHEL is the better choice in Production environments I have chosen to focus on using one of the forks that is available for free. Zabbix is supported on Ubuntu, Debian, Suse, Raspberry .... and it can be compiled on any OS that is Unix based so it's almost impossible to cover them all. However the book is Opensource and in GIT so feel free to contribute the code for your favorite flavour :). I will use [Rocky Linux](https://rockylinux.org/) 9 in this book, but it should work for most of the other installations as well.

## What version of Zabbix is used in this book ?

Since we are almost at the release of Zabbix 7, I will focus on version 7 since it will be the new LTS. It should also apply to most other versions but of course there will be minor changes. In the future, if there is enough support from the community to update this book together, it would be great if we could build a book for every LTS version available.


## How to use this book ?

The book will try to cover all the topics, feel free to let me know if something is missing or feel free to make a pull request. 
There is no need to start from page 1 and read the book till the end. Some people will be looking for basic knowledge others might want to skip to the fun part, so I want the book to be useful for everyone. Therefor I will try to explain as best as possible in every topic the exact steps needed to reproduce.
 
There will be moments in the book where you need to type some code, I will show the commands you need to type in a box just like here.

```
## some command 
```

Notes to some useful documentation will be added at the bottom of the page.


Here is a simple footnote[^1]. With some additional text after it.

[^1]: My reference.


In case there is some important information to share I will add notes in the documentation like can be seen here :

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


