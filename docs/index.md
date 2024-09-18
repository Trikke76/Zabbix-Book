# What is this book about ?

Welcome, and thank you for your interest in this Zabbix guide.

In the past, I authored the [Zabbix Cookbook](https://www.packtpub.com/product/zabbix-cookbook/9781784397586)
and co-authored [Zabbix 4 Network Monitoring](https://www.packtpub.com/product/zabbix-4-network-monitoring-third-edition/9781789340266)
with Richards, both published by Packt.

![Zabbix Cookbook](images/book1.png){width=200} ![Zabbix 4 Network Monitoring](images/book2.png){width=200}

The _Zabbix Cookbook_ was one of the first comprehensive resources for Zabbix users,
though it's now somewhat outdated. It has since been succeeded by the
[Zabbix 7 IT Infrastructure Monitoring Cookbook](https://www.packtpub.com/product/zabbix-7-it-infrastructure-monitoring-cookbook-third-edition/9781801078320),
written by Brian and Nathan—two individuals I highly recommend and enjoy
collaborating with.

Packt offers a wide range of other Zabbix-related books.
You can browse the full collection
[here](https://www.packtpub.com/search?query=zabbix). Additionally,
for those looking for non-English titles, Amazon provides Zabbix books in languages
such as Chinese, Spanish, and more, published by Packt and other publishers.
Find them [here](https://www.amazon.com/s?k=zabbix&crid=3G0JRTVTKS7YU&sprefix=zabbix+%2Caps%2C167&ref=nb_sb_noss_2).

As Zabbix is an open-source product, my goal was never to make money from the
books I wrote.
This led me to reflect on how I could approach things differently.
I wanted to create a book that would be freely available and updated with each
new version of Zabbix.

Being a strong advocate of documentation in Markdown or AsciiDoc, I decided to
share the book in a Git repository, using Markdown as the format.
The only challenge left was how to present those Markdown files in a reader-friendly
way, like a traditional book.
After some research, I discovered [MkDocs](https://www.mkdocs.org), a Python-based
library that converts Markdown files into HTML and can be templated.
With MkDocs, the problem was solved, and a new book was born.

## Who Am I?

My name is Patrik Uytterhoeven, and I work for a Belgian company called Open-Future.
I joined Open-Future in January 2013, and that's when my journey with Zabbix began.
The company provided me with the opportunity to grow my expertise and become a
certified Zabbix trainer. As of this year, I’ve been a Zabbix trainer for 10 years.

If you're interested in attending one of my trainings, you can register at [www.open-future.be](https://www.open-future.be).
You might wonder, "Why take a training if I can read this book for free?"
While books provide extensive information on setup and configuration,
trainings offer valuable insights, tips, and feedback that you won’t find easy
in a book. Books simply can't cover every detail.

## What Operating System Do I Need?

As someone who primarily works with RHEL-based systems,
I believe RHEL is the optimal choice for production environments. For this reason,
I’ve chosen to focus on one of its free forks. Zabbix is supported on Ubuntu,
Debian, SUSE, Raspberry Pi, and other systems, and it can be compiled on any
Unix-based OS. However, it's nearly impossible to cover every OS in this book.
Since the book is open-source and hosted on Git, feel free to contribute code
for your preferred distribution!
In this book, I will use [Rocky Linux](https://rockylinux.org/) 9,
but the instructions should apply to most other installations as well.

## What Version of Zabbix Is Used in This Book?

This book focuses on Zabbix 7.0, as it is the latest LTS (Long Term Support)
release.
Most of the content will be relevant to other versions, though minor changes
may exist. In the future, if the community supports updating this book,
it would be fantastic to create a version for every LTS release.

## How to Use This Book

This book is designed to cover a wide range of topics.
If you feel something is missing, please reach out or make a pull request.

You don’t need to start at page one and read through to the end.
Some readers may seek basic knowledge, while others might want to skip directly
to more advanced topics.
I aim to make this book useful for everyone by providing detailed explanations
and clear steps for each topic.

Throughout the book, you'll encounter code snippets. These will be presented
in code blocks like the one below:

```bash
# sudo yum install zabbix-server-mysql
```

Notes to some useful documentation will be added at the bottom of the page.

Here is a simple footnote[^1]. With some additional text after it.

[^1]: My reference.

In case there is some important information to share I will add notes in the
documentation like can be seen here :

???+ note
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla.
    Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa,
    nec semper lorem quam in massa.

???+ info
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla.
    Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa,
    nec semper lorem quam in massa.

???+ tip
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla.
    Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa,
    nec semper lorem quam in massa.

???+ question
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla.
    Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa,
    nec semper lorem quam in massa.

???+ warning
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla.
    Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa,
    nec semper lorem quam in massa.

???+ bug
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla.
    Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa,
    nec semper lorem quam in massa.

???+ example
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla.
    Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa,
    nec semper lorem quam in massa.
