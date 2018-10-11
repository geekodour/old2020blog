---
title: "Setting Up Nagios 4 on arch linux with nginx"
date: 2018-09-19T03:52:20+05:30
draft: true
categories:
  - Arch Linux
  - Nagios
slug: setting-up-nagios
---

This is a reference guide on installing and configuring nagios on Arch Linux, this guide will be similar for other operating systems too.
Except the package manager commands. As this blog post is written as part of fsf internship so instructions for both nagios 3 and 4 are given

For the record,
the following commands have been tested on Arch Linux 4.18.8-arch1-1-ARCH and also on Trisquel 8.

Create the users, and add the soft links
```
$ sudo adduser --system --no-create-home --disabled-login --group nagios
```
plugin install
```
$ ./configure --with-nagios-user=nagios --with-nagios-group=nagios
```

First thing if you don't know what Nagios, you might want to read [introduction to systems monitoring.]()
There are a various ways and Nagios is just one of the tool.

First download nagios from [here](https://support.nagios.com/kb/article/nagios-core-installing-nagios-core-from-source-96.html#Arch_Linux)
Untar it.
```
./configure --prefix=/usr/local/nagios-4.4.1 --with-command-group=nagcmd
```
For this we need a nagios user and two groups to run our commands.

This guide supposes that you have selinux disabled

Added custom log directory in `/var/log/nagios`

Till now no plugins are installed, but let's setup nginx.

Interesting thing about nagios is that it uses cgi and not fast cgi

Setup nginx, where plugins bro?
- in /usr/local/nagios/plugins in nagios3
- in /usr/local/nagios/libexec in nagios4
They are different, I've no idea why they would do something like that.

-----------
For Nagios 3, it's very easy.


1. Go to https://www.nagios.org/downloads/nagios-core/thanks/?skip=1&product_download=nagioscore-source
and download the version of nagios you want.



-----
- Prometheus main benifit is Time series data.
- Usually nagios has the polling interval set to >1 min
  - No internal means of storing time series data
  - No built in support for displaying time series data
  - pnp4nagios does solve this by handling perf data
