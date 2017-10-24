---
layout: post
title: How to Install HassIO and Pi-Hole on a Single Raspi
date: 2017-10-23 8:00:00
author: Neal Dawson-Elli
---

resources used:
install raspbian with ssh enabled: https://hackernoon.com/raspberry-pi-headless-install-462ccabd75d0
install hassio once raspbian is installed: https://www.reddit.com/r/homeassistant/comments/6x41ns/multipurpose_pi_with_hassio_running_cups_pihole/dmde2wp/

steps:
install hassbian lite
change the password using sudo raspi-config
find your ip address using ip -4 addr show dev eth0 | grep inet (not useful because we already knew it from the DHCP client table)
set static ip address using:
nano /etc/dhcpcd.conf
un-comment the following lines:
       interface eth0
       static ip_address=10.1.1.30/24
       static routers=10.1.1.1
       static domain_name_servers=10.1.1.1
install hass.io
curl -sSL https://get.docker.com | sh
install other dependencies listed here:
https://github.com/home-assistant/hassio-build/blob/master/install/README.md
sudo apt-get install socat
sudo apt-get install jq
sudo su
curl -sL https://raw.githubusercontent.com/home-assistant/hassio-build/master/install/hassio_install | bash -s -- -m raspberrypi3
sudo shutdown -r now
takes ~10-20 minutes to reboot
verify that hass.io works - the address may be raspberrypi.local:8123 instead of hassio.local:8123
install pi-hole
curl -sSL https://install.pi-hole.net | bash

now that they both work, what should we do with hass.io?
add-ons:
samba
mosquitto
ssh
google assistant

