# Autosshtunns
Manageable persistent groups ssh tunnels Systemd service

## Description
It is an easy to configure Systemd service that handles persistant groups of SSH tunnels using autossh.
Each configuration file, /etc/autosshtunns/*.conf allow to set up a single persistent background ssh
connection carrying a group of forwarded ports from client side to server network's side. This is what
we define as a "forwarding group". Forwarding groups may include both local-to-hostport and dynamic
forwards.

## Basic workflow
* On Start
  1. A system administrator/operator issues "systemctl start autosshtunns@group";
  2. Systemd loads to environment values defined in /etc/autosshtunns/group.conf;
  3. Systemd issues the /usr/local/bin/autosshtunns script;
  4. Autosshtunns script assembles an autossh options string using environment;
  5. Autosshtunns executes the autosshtunns w/options string to monitor and keeps the underlying
     ssh connection that keeps the forwarded group of tunnels up.
* On Stop
  1. A system admin/operator issues "systemctl stop autosshtunns@group";
  2. Systemd sends a SIGTERM signal to corresponding PID.

## Requirements
Autosshtunns is a Linux Systemd based service consisting of the "autosshtunns" shell script and the
"autosshtunns@.service" unit file that integrates below software components:
* CentOS/RHEL or Ubuntu: Tested on version 7 and 16.04 respectively.
* Shell                : Developed for Dash, trying to be Posix complaint and tested Bash4
* Systemd              : Tested with release 229
* Ssh Client           : Tested for OpenSSH_6.6.1p1 and OpenSSH_7.2p2
* SSh server           : Tested for OpenSSH_6.6.1p1
* Autossh              : Tested for autossh 1.4e

## Installation
1. Create a system account for the service:
   * In Ubuntu you may use
     a. addgroup --system asshtunns
     b. adduser --system --ingroup asshtunns --shell /bin/sh --home /home/asshtunns asshtunns
     c. passwd asshtunns
   * In for other distributions
     a. groupadd -r asshtunns
     b. useradd -r autosshtunns -g $gid -s /bin/sh -d /home/autosshtunns autosshtunns
2. Create ssh client resources
   a. install -o asshtunns -g asshtunns -m 700 -d /home/asshtunns/.ssh/ctrlmasters
   b. touch /home/asshtunns/.ssh/known_hosts
   c. ssh-keygen -t rsa -b 2048 -C "asshtunns@NOCSrvAdmDesk01" -f /home/asshtunns/.ssh/id_rsa
   d. chown -vR asshtunns:asshtunns /home/asshtunns/.ssh
3. Install autosshtunns script
  a. Copy autosshtunns script file to /usr/local/bin
  b. Set owner and permissions
     * chown -v asshtuns:root /usr/local/bin/autosshtunns
     * chmod -v 550 /usr/local/bin/autosshtuns
4. Install configuration files
   * install -o asshtunns -g root -m 770 -d /etc/autosshtunns
   * Copy all your group.conf files in previous dir
   * chmod -v 660 /etc/autosshtunns/*
5. Export asshtunns ssh public keys to all $_HOSTNAME hosts found in each /etc/autosshtunns/*.conf file
6. Install autosshtunns@.service in /etc/systemd/system
7. Reload systemd manager configuration with "systemctl daemon-reload"
8. For each /etc/autosshtunns/*.conf file:
   * systemctl enable autosshtunns@tunnsgroup # Enable on-startup execution
   * systemctl start autosshtunns@tunnsgroup  # Start the forwarding service right now

## Adding new forwarding groups
1. Create a new /etc/autosshtunns/newgrp.conf file using tunnsgrp.conf as template
   * cp -iv /etc/autosshtunns/tunnsgrp.conf /etc/autosshtunns/newgrp.conf
2. Edit newgrp.conf according to your requirements
3. Enable noew automatic forwarding group service on startup
   * systemctl enable autosshtunns@newgrp
4. Start and stop the service as needed using systemctl

