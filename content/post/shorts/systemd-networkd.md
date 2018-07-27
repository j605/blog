---
title: "Moving to systemd-networkd"
date: 2018-07-27T01:04:20+02:00
tags:
  - archlinux
categories:
  - dgplug
draft: true
---

`systemd-networkd` is a network manager that comes built in with Systemd. It has
features, similar to [NetworkManager](https://wiki.archlinux.org/index.php/NetworkManager) and
[ConnMan](https://wiki.archlinux.org/index.php/ConnMan) which are more commonly used in
distributions like Ubuntu and Fedora.

# My old system
I had moved away from `NetworkManager` one year back due to constant issues with connecting
to enterprise Wi-Fi with logs and errors that were difficult to debug or fix. It was also
difficult to see where the failures occurred prompted me to move towards a more modular
system.

The hacked system did not have a manager on top and it was cobbled together with:

1. `wpa_supplicant` for wireless.
2. `dhcpcd` for DHCP (wired and wireless).
3. `dnscrypt-proxy` for DNS over HTTPS and as a local cache.

Last week I started having problems when `dhcpcd` was not setting the proper network
gateway and I couldn't figure out what exactly was the issue since there were no
updates to any of the affected programs. I realized that decoupling DHCP caused
issues that can only be solved with a program that can react to network link changes properly.
To be honest, this system did not have any issues for a long time and I could've just
continued to use it after figuring out the problem but I thought it was time to try
`networkd`.

# `systemd-networkd`
It works similarly to how Systemd handles services. `networkd` uses INI style
".network" configuration files. Look at the man page for `systemd.network` for more
information.

My motivation was to seamlessly handle wired and wireless interfaces without dropping
connection due to state changes. For this purpose I had two files,
`/etc/systemd/network/20-wired.network`

```INI
[Match]
Name=eno1

[Network]
DHCP=yes

[DHCP]
RouteMetric=10
```

and `/etc/systemd/network/25-wireless.network` with
```INI
[Match]
Name=wlp2s0b1

[Network]
DHCP=yes

[DHCP]
RouteMetric=20
```

You should start `systemd-networkd.service` and it should work with your existing
`wpa_supplicant@wlp2s0b1.service` which should be setup separately.

My DHCP issue was solved by `networkd` knowing about both networks and handling them
properly. The other nice property is I can set route metrics in the configuration
which puts the kernel in charge of using the appropriate route when there are multiple
gateways. Example of `ip route` with both interfaces connected:

```sh
$ ip ro
default via 192.168.1.1 dev eno1 proto dhcp src 192.168.1.125 metric 10
default via 10.46.255.254 dev wlp2s0b1 proto dhcp src 10.46.140.105 metric 20
10.46.128.0/17 dev wlp2s0b1 proto kernel scope link src 10.46.140.105
10.46.255.254 dev wlp2s0b1 proto dhcp scope link src 10.46.140.105 metric 20
192.168.1.0/24 dev eno1 proto kernel scope link src 192.168.1.125
192.168.1.1 dev eno1 proto dhcp scope link src 192.168.1.125 metric 10
```

## Handling DNS
Systemd also comes with `resolved` which has a bunch of nice features and also integrates
with options in the network files. However tempting this might be I still wanted to
stick with my working `dnscrypt-proxy` setup. This is easily done by asking `resolved`
to use `127.0.0.1` as its DNS server in `/etc/systemd/resolved.conf`. Now resolv.conf
should have use `127.0.0.53` which is the `resolved` stub and it delegates the actual
work to `dnscrypt-proxy`. 

While you may feel, this is similar to `NetworkManager` I would point out that `wpa_supplicant`
is handled separately and hence affords more ways to test your wireless connection before
getting caught in an endless loop. Although if `NetworkManager` works for you, there isn't a compelling reason to
leave it. In the end, this was pretty easy to set up and the system looks more robust now.
