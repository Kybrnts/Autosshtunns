# Autosshtunns
Manageable persistent ssh tunnels linux service

# Description

# Requirements
autossh 1.4e (tested)
OpenSSH_6.6.1p1, OpenSSL 1.0.1e-fips (tested)

# Installation
addgroup --system asshtunns
adduser --system --ingroup asshtunns --shell /bin/sh --home /home/asshtunns asshtunns
passwd asshtunns
install -o asshtunns -g asshtunns -m 700 -d /home/asshtunns/.ssh/ctrlmasters
touch /home/asshtunns/.ssh/known_hosts
ssh-keygen -t rsa -b 2048 -C "asshtunns@NOCSrvAdmDesk01" -f /home/asshtunns/.ssh/id_rsa
chown -vR asshtunns:asshtunns /home/asshtunns/.ssh

Install autosshtunns script in /usr/local/bin/
chown -v asshtuns:root /usr/local/bin/autosshtunns
chmod -v 550 /usr/local/bin/autosshtuns

install -o asshtunns -g root -m 770 -d /etc/autosshtunns
Copy all tunnsgroup.conf files in previous dir
chmod -v 660 /etc/autosshtunns/*
Export asshtunns ssh public keys to all $_HOSTNAME hosts found in each tnnsgroup.conf

Install autosshtunns@.service in /etc/systemd/system
systemctl daemon-reload
For each tunnsgroup.conf file:
* systemctl enable autosshtunns@tunnsgroup
* systemctl start autosshtunns@tunnsgroup

# Adding new local forwarded services
