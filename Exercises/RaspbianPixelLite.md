# Rasberry Pi Procedures and Notes

<!-- TOC -->

- [Rasberry Pi Procedures and Notes](#rasberry-pi-procedures-and-notes)
- [Base Build Procedure](#base-build-procedure)
    - [Source ISO](#source-iso)
    - [First Running](#first-running)
        - [Configure Keyboard](#configure-keyboard)
        - [raspi-config](#raspi-config)
        - [Wireless](#wireless)
            - [/etc/network/interfaces](#etcnetworkinterfaces)
        - [Get IP Address and Updates](#get-ip-address-and-updates)
        - [Disable Console Blanking](#disable-console-blanking)
    - [Initial Software](#initial-software)
        - [NTP](#ntp)
    - [Optional Utility Software](#optional-utility-software)
        - [Pre-cleaning](#pre-cleaning)
        - [Git](#git)
            - [Configure git for Serving Repositories](#configure-git-for-serving-repositories)
                - [Server](#server)
                - [Clients](#clients)
        - [Python](#python)
- [X11 and LXDE Installation](#x11-and-lxde-installation)
    - [X11 XServer](#x11-xserver)
    - [LXDE](#lxde)
    - [LightDM](#lightdm)
        - [Remote Control Software](#remote-control-software)
    - [Utility Software](#utility-software)

<!-- /TOC -->

---

# Base Build Procedure

## Source ISO

* [Raspbian Download Page](https://www.raspberrypi.org/downloads/raspbian/)
* [Raspbian Jessie Lite Image Download](https://downloads.raspberrypi.org/raspbian_lite_latest)

## First Running

### Configure Keyboard

* Edit **`/etc/default/keyboard`**

```sh
XKBLAYOUT="us"
```

### raspi-config

* *Localisation Options*
    * *Change Locale*
        * Unset **en_GB**
        * Set **en_US.UTF-8 UTF-8**
    * *Change Timezone*
* *Interfacing Options*
    * Enable **ssh**
* *Advanced Options*
    * **Expand Filesystem**

### Wireless

* [Reference](http://www.raspberrypi.org/phpBB3/viewtopic.php?f=28&t=44044)

#### /etc/network/interfaces

```sh
sudo nano /etc/network/interfaces
allow-hotplug wlan0
iface wlan0 inet dhcp
wpa-ssid ******
wpa-psk ******
```

### Get IP Address and Updates

```sh
ip address
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install ca-certificates
```

### Disable Console Blanking

```sh
sudo nano /etc/kbd/config
BLANK_TIME=0
BLANK_DPMS=off
POWERDOWN_TIME=0
```

## Initial Software

### NTP

Network Time Protocol *NTP* is used to synchronize the computer's time clock with reference time server.

* Reference: [Install and Configure NTP in Debian](http://www.tecmint.com/install-and-configure-ntp-server-client-in-debian/)
* Reference: [NIST Internet Time Servers](http://tf.nist.gov/tf-cgi/servers.cgi)

```sh
# Install NTP
sudo apt-get install ntp ntpdate -y
# Optionally modify the time servers and other ntp options
sudo nano /etc/ntp.conf
# Apply configuration changes
sudo dpkg-reconfigure ntp
# Check
sudo ntpd --version
sudo ntpq -p
sudo service ntp status
```

## Optional Utility Software

```sh
sudo apt-get install htop
```

### Pre-cleaning

Pre-cleaning is optional. It should be done before we build and install any software without the *aptitude* package manager.

```sh
# remove previous python versions
sudo apt-get purge python
```

### Git

```sh
# Install Git prerequisites
sudo apt-get install build-essential gettext libssl-dev libcurl4-openssl-dev libexpat1-dev tk-dev asciidoc xz-utils -y
# sudo apt-get install docbook2x unzip -y

# Change to Home folder
cd ~
# Get git
wget https://www.kernel.org/pub/software/scm/git/git-2.13.3.tar.gz 
tar -xzvf git-2.13.3.tar.gz
cd git-2.13.3
# Make Git and Install 
make prefix=/usr/local all 
# make prefix=/usr/local all doc info
sudo make prefix=/usr/local install all 
# sudo make prefix=/usr/local install all install-doc install-html install-info
# Test Git
git -–version
```

#### Configure git for Serving Repositories

##### Server
```sh
# For gogs 'git' user:
# sudo adduser --disabled-login --gecos 'Gogs' git
# For simple 'git' user:
sudo adduser --disabled-login git
sudo -u git -H mkdir /home/git/.ssh
sudo -u git -H vi /home/git/.ssh/authorized_keys
# Server sied Git Repository Initialization
# sudo git init /opt/git/MyRepoExample.git --share --bare
```

##### Clients

```sh
# SSH Clients need to do this:
ssh-keygen
# Append the contents from ~/.ssh/id_rsa.pub to the server's authorized_keys file.
```

### Python

```sh
# remove previous python versions
sudo apt-get purge python
# Python 2
cd ~/
wget https://www.python.org/ftp/python/2.7.13/Python-2.7.13.tar.xz
tar -xvf Python-2.7.13.tar.xz
cd Python-2.7.13
./configure
make
sudo make install
python2 --version
# Python 3
cd ~/
wget https://www.python.org/ftp/python/3.6.2/Python-3.6.2.tar.xz 
tar -xvf Python-3.6.2.tar.xz
cd Python-3.6.2
./configure
make
sudo make install
python3 --version
# now check to see the default python version
sudo reboot
python --version
```

# X11 and Pixel Installation

GUI installation instructions are derived from the procedure described in: [GUIDE Raspbian Lite with LXDE/XFCE/MATE/Openbox GUI](https://www.raspberrypi.org/forums/viewtopic.php?f=66&t=133691)

## X11 XServer

```sh
sudo apt-get install xserver-xorg
sudo apt-get install xinit x11-xserver-utils xorg
## Raspbian: if physical mouse and keyboard no longer working apt-get install xserver-xorg-input-evdev
sudo reboot
```

## Pixel

```sh
# Raspbian PIXEL
# sudo rpi-update
sudo apt-get install raspberrypi-ui-mods
sudo reboot
```

## LightDM

```sh
sudo nano /etc/lightdm/lightdm.conf
  autologin-user=theusername
  autologin-user-timeout=0
sudo dpkg-reconfigure lightdm
```

### Remote Control Software

* [x11vnc Server](http://www.karlrunge.com/x11vnc/)

```sh
sudo apt-get install x11vnc
```

* [ssvnc Karlrunge VNC viewer client](http://www.karlrunge.com/x11vnc/ssvnc.html)

## Utility Software

```sh
sudo apt-get install lxterminal leafpad clipit -y
sudo apt-get install freerdp-x11
```

---

Jul 28, 2017 updated by [//AJ](http://atc-gogs.accuratetool.com:3000/aj)
