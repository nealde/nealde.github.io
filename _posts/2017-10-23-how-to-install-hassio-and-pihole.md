---
layout: post
title: How to Install HassIO and Pi-Hole on a Single Raspi
date: 2017-10-26 8:00:00
author: Neal Dawson-Elli
---



## Steps:
1. Install [Raspbian](https://www.raspberrypi.org/downloads/raspbian/) with SSH enabled
2. Find the Raspi on your network using a DHCP client table
3. Change the password to the device
4. Set up static IP
5. Install [HassIO](https://home-assistant.io/hassio/)
6. Verify that the installation was a success
7. Install [Pi Hole](https://pi-hole.net/)
8. Verify that Pi Hole works properly
9. Customize HassIO

Before we begin, this project would not have been possible without these two resources:
install raspbian with ssh enabled: https://hackernoon.com/raspberry-pi-headless-install-462ccabd75d0
install hassio once raspbian is installed: https://www.reddit.com/r/homeassistant/comments/6x41ns/multipurpose_pi_with_hassio_running_cups_pihole/dmde2wp/

### 1. Installing Raspbian Lite

To begin, download [Raspbian Lite](https://www.raspberrypi.org/downloads/raspbian/) as a .zip and extract it to a folder.
![png]({{ site.baseurl }}/img/phhassio/raspbian.PNG)

Then, download and install [Etcher](https://etcher.io/) and image the .zip to the SD card following the instructions.

Once that is done, on the SD card, add a blank, extensionless file called 'SSH', as seen below:

This can be done by creating a blank text file, renamind it to SSH, and deleting the extension.

Now, plug the SD card into the Raspi and boot it up, ensuring that it has an internet connection.  A hard line connection is recommended for this step.

### 2. Finding the Raspi's IP Address

Next, log into your router (mine is at `192.168.1.1`) and navigate to the DHCP client table.  There should be a raspberry pi connected with an associated IP address.
Once we know this, we can begin to SSH into it.

### 3. Change the Raspi's Password

Using [PuTTY](http://www.putty.org/), we can SSH into the Raspi by typing the IP address into the Host Name box:

We can save this session for later by typing a name into the Saved Sessions box and selecting save.
The default username and password for a Raspi are:
```
username: pi
password: raspberry
```

We change the password by running the [command](https://www.raspberrypi.org/documentation/configuration/raspi-config.md):
```
sudo raspi-config
```
and following the prompts for changing the password.  

### 4. Setting a Static IP address

There are [several methods](https://raspberrypi.stackexchange.com/questions/37920/how-do-i-set-up-networking-wifi-static-ip-address) for changing your IP 
address to static, which is required in order to use it as a DNS (via Pi Hole).  Since we are using the `eth0` interface, we can simply type the following into the command prompt:
```
nano /etc/dhcpcd.conf
```
and edit the file to say the following:
```
 # Here is an example which configures a static address, routes and dns.
       interface eth0
       static ip_address=192.168.1.136/24 # your IP goes here
       static routers=192.168.1.1
       static domain_name_servers=8.8.8.8
```

Then, we close the file and save it using `x` and pressing `y`.  We then reboot the Raspi using the following command:
```
sudo shutdown -r now
```

Once it reboots, we can SSH again, hopefully into the same IP address as last time, as long as the static IP we assigned is the same as the old IP address.

With that, we're ready to install HassIO!

### 5. Installing HassIO

Because Linux is the best, simply run the following commands to install HassIO:
First, install docker:
```
curl -sSL https://get.docker.com | sh
```
Then, install other [dependencies](https://github.com/home-assistant/hassio-build/blob/master/install/README.md) by running these commands one at a time:
```
sudo apt-get install socat
sudo apt-get install jq
sudo su
curl -sL https://raw.githubusercontent.com/home-assistant/hassio-build/master/install/hassio_install | bash -s -- -m raspberrypi3
```
Then, reboot using
```
sudo shutdown -r now
```

The Raspi will take ~10-20 minutes to reboot as it downloads HassIO and installs it.

### 6. Verify that HassIO Has Been Installed

The default HassIO location is `hassio.local:8123`, but since we installed it atypically, we need to access it via `raspberrypi.local:8123`.  You may also use the IP address, if you so desire.

If everything works, the page should look like this:
```
```

### 7. Install Pi Hole

Why install Pi Hole?  Well, if your Raspi is going to be on all the time running your HassIO server, it might as well also block ad traffic for your whole network!

As [Pi hole](https://pi-hole.net/)'s website tells you, piping to bash can sometimes be a risky maneuver, but we're a trusting bunch, so we blindly follow instructions, typing:
```
curl -sSL https://install.pi-hole.net | bash
```

Once the installation finishes, we need to set the Raspi up as the DNS for our router, to route all traffic through it.  This is fairly straightforward - simple log into your router 
(mine is at `192.168.1.1`), and set your first DNS to your Raspi's IP address.  Then, set a 2nd failover DNS to the default DNS, typically Google  at `8.8.8.8` or [OpenDNS](https://www.opendns.com/).


### 8. Verifying Pi Hole

Once that's done, we can go to an ad-riddled webpage, say [Yahoo](https://www.yahoo.com/), which has at least 2 ads right at the top:

![png]({{ site.baseurl }}/img/phhassio/yahoo.PNG)

If yours doesn't have those ads, congratulations! It installed successfully!

We can see Pi Hole's main page, and see how much traffic has been blocked, by going to `raspberry.local/admin`

![png]({{ site.baseurl }}/img/phhassio/pihole_admin.PNG

### 9. Customizing HassIO

There are many great resources for customizing HassIO, and ony of my favorites is [BRUH Automation](https://www.youtube.com/channel/UCLecVrux63S6aYiErxdiy4w) 
- his videos are well-produced, informative, and got me started on home automation!

In particular, [this](https://www.youtube.com/watch?v=XWPluWcYRMI) video is a great into the HassIO, although the installation method differs from ours.
Some add-ons that I installed right out of the gates include:
samba
mosquitto
ssh
google assistant

