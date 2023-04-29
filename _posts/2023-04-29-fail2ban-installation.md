---
title: How To Install and Use Fail2Ban on Ubuntu 22.04
date: 2023-04-29 13:00:00 +200
categories: [homelab,fail2ban,ubuntu]
tags: [fail2ban,linux]
---

# Introduction

In this tutorial we will install and use Fail2Ban runtime on Ubuntu 22.04. We will start off by installing the package and configuring a sample jail to monitor the SSH agent.

## Prerequisites

To follow this tutorial, the following are required:

1. One Ubuntu 22.04 server, including a sudo non-root user  

# Installation Steps

## Installing Fail2Ban

Fail2ban is available in Ubuntu’s software repositories. Begin by running the following commands as a non-root user to update your package listings and install Fail2ban:

```shell
sudo apt update
sudo apt install fail2ban
```
Fail2ban will automatically set up a background service after being installed. However, it is disabled by default, because some of its default settings may cause undesired effects. You can verify this by using the systemctl command:

```shell
systemctl status fail2ban.service
```

```text
Output
○ fail2ban.service - Fail2Ban Service
     Loaded: loaded (/lib/systemd/system/fail2ban.service; disabled; vendor preset: enabled
     Active: inactive (dead)
       Docs: man:fail2ban(1)
```

You could enable Fail2ban right away, but first, you’ll review some of its features.

## Configuring Fail2Ban

The fail2ban service keeps its configuration files in the /etc/fail2ban directory. There is a file with defaults called jail.conf. Go to that directory and print the first 20 lines of that file using head -20:

```shell
cd /etc/fail2ban
head -20 jail.conf
```
```text
Output
#
# WARNING: heavily refactored in 0.9.0 release.  Please review and
#          customize settings for your setup.
#
# Changes:  in most of the cases you should not modify this
#           file, but provide customizations in jail.local file,
#           or separate .conf files under jail.d/ directory, e.g.:
#
# HOW TO ACTIVATE JAILS:
#
# YOU SHOULD NOT MODIFY THIS FILE.
#
# It will probably be overwritten or improved in a distribution update.
#
# Provide customizations in a jail.local file or a jail.d/customisation.local.
# For example to change the default bantime for all jails and to enable the
# ssh-iptables jail the following (uncommented) would appear in the .local file.
# See man 5 jail.conf for details.
#
# [DEFAULT]
```
As you’ll see, the first several lines of this file are **commented out** – they begin with *#* characters indicating that they are to be read as documentation rather than as settings. As you’ll also see, these comments are directing you not to modify this file directly. Instead, you have two options: either create individual profiles for Fail2ban in multiple files within the jail.d/ directory, or create and collect all of your local settings in a jail.local file. The jail.conf file will be periodically updated as Fail2ban itself is updated, and will be used as a source of default settings for which you have not created any overrides.

In this tutorial, you’ll create jail.local. You can do that by copying jail.conf:
```shell
sudo cp jail.conf jail.local
```
Now you can begin making configuration changes. Open the file in nano or your favorite text editor:
```shell
sudo nano jail.local
```
While you are scrolling through the file, this tutorial will review some options that you may want to update. The settings located under the [DEFAULT] section near the top of the file will be applied to all of the services supported by Fail2ban. Elsewhere in the file, there are headers for [sshd] and for other services, which contain service-specific settings that will apply over top of the defaults.
```shell
/etc/fail2ban/jail.local
[DEFAULT]
. . .
bantime = 10m
. . .
```
The bantime parameter sets the length of time that a client will be banned when they have failed to authenticate correctly. This is measured in seconds. By default, this is set to 10 minutes.
```shell
/etc/fail2ban/jail.local
[DEFAULT]
. . .
findtime = 10m
maxretry = 5
. . .
```
The next two parameters are findtime and maxretry. These work together to establish the conditions under which a client is found to be an illegitimate user that should be banned.

The maxretry variable sets the number of tries a client has to authenticate within a window of time defined by findtime, before being banned. With the default settings, the fail2ban service will ban a client that unsuccessfully attempts to log in 5 times within a 10 minute window.


# References


Article Reproduced from: https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-22-04