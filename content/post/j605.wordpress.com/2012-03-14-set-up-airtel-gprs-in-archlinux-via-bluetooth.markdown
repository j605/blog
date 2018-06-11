---
author: j605
comments: true
date: "2012-03-14T16:16:13Z"
link: https://j605.wordpress.com/2012/03/14/set-up-airtel-gprs-in-archlinux-via-bluetooth/
slug: set-up-airtel-gprs-in-archlinux-via-bluetooth
tags:
- airtel
- archlinux
- bluetooth
- gprs
title: Set up airtel gprs in archlinux via bluetooth
wordpress_id: 33
---

I had an urge to write this post since setting up a dial-up connection took me quite an effort. First of all you should have bluez and wvdial installed.

```sh
sudo pacman -S bluez
sudo pacman -S wvdial
```

First scan for bluetooth-enabled devices by


```sh
hcitool scan
```


If there are any devices, its address and name would be displayed. Copy the required address. Now, create rfcomm device to connect to your phone via bluetooth. 


```sh
sudo mknod --mode=666 /dev/rfcomm0 c 216 0
```


In the file /etc/conf.d/bluetooth, set RFCOMM_ENABLE true. In another terminal run bluez-simple-agent and now to check the connection, type 


```sh
rfcomm connect 0 address channel
```


You already have the address, the active channel is obtained from the command 


```sh
sdptool search dun | grep Channel
```


When you run the command, you will be prompted for a pin in your mobile and that has to be entered in the terminal which is running bluez-simple-agent. You should have successfully connected right now. Now press ^C to exit the connection. For setting up the internet connection you have to edit /etc/wvdial.conf or create one. For guys using a different ISP, get your APN by googling(just change airtelgprs.in by your ISP's APN).


```ini
[Dialer Defaults]
Modem = /dev/rfcomm0 
Init3 = AT+cgdcont=1,"IP","airtelgprs.com"
Phone = *99# 
Username = user
Password = pass
New PPD = yes
ISDN = no
BAUD = 115200
Stupid Mode = yes
Carrier Check = no
```


The Username and Password, in the case of airtel are just for namesake. You can fill them up with bogus data. With that done restart the bluetooth daemon.


```sh
sudo rc.d restart bluetooth
```


Starting wvdial as root would connect you to the internet.
