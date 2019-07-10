---
title: "Using DNSOverTLS With Unbound"
date: 2019-07-10T11:18:06+02:00
tags:
  - linux
  - dns
categories:
  - dgplug
---
Firstly, DNS is how web addresses are looked up to find their location in terms of IP addresses.
So it is actually surprising that this lookup has always been unencrypted.
The issue has become mainstream of sorts when UK's Internet Service Provider's Association nominated Firefox as an [internet villain](https://techcrunch.com/2019/07/05/isp-group-mozilla-internet-villain-dns-privacy/) for enabling DNSOverHTTPS (DoH).
This allowed people to bypass DNS based blocking performed by ISPs which further cemented the idea that one needs encrypted DNS to avoid being censored or worse [MITMed](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) by an authoritarian government by using domain level blocking.
I prefer DoT over the DoH protocol simply because of latency issues caused by dependency on TCP for the latter.
DoT works similar to regular DNS over UDP but under a different port.
One reason for vendors to prefer DoH is due to over eager firewalls blocking all ports except 80 and 443 used by HTTP and HTTPS by default.
Okay, lets get to the crux of the article!

# Install and configure unbound
Unbound is usually available in the repositories of operating systems, Linux and BSD.
It is therefore advisable to get it from your package manager.

The next step is to configure unbound to use root servers to resolve DNS recursively.
This is useful to first get acquainted with unbound and also to rule out issues if DoT fails to work.
```yaml
# /etc/unbound/unbound.conf
server:
  verbosity: 1
  use-syslog: yes # outputs to systemd journal in our case
  do-daemonize: no # handled by systemd
  username: "unbound" # use separate user for security
  directory: "/etc/unbound"
  auto-trust-anchor-file: trusted-key.key # Auto-update the trust anchors, used for DNSSEC
 root-hints: root.hints # download root.hints from https://www.internic.net/domain/named.cache
```
It is important to take care of the permission of the directory so unbound can read and write to it.
You may have to manually change ownership of `/etc/unbound` using `chown`.
Before starting the service, make sure to change `/etc/resolv.conf` to use the local nameserver run by unbound.
```conf
# /etc/resolv.conf
nameserver 127.0.0.1
```
This step also requires some care if your distribution is handling this using `NetworkManager`, `systemd-resolved` or `openresolvconf`.
Refer to your distribution specific documentation on how to handle this if you are unsure.
After this is done, start the unbound service and check the logs for errors if any.
You can use `drill` or `dig` to do DNS testing from the terminal. For example,
```
> drill lobste.rs
;; ->>HEADER<<- opcode: QUERY, rcode: NOERROR, id: 42947
;; flags: qr rd ra ; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0 
;; QUESTION SECTION:
;; lobste.rs.   IN      A

;; ANSWER SECTION:
lobste.rs.      3600    IN      A       71.19.148.33

;; AUTHORITY SECTION:

;; ADDITIONAL SECTION:

;; Query time: 400 msec
;; SERVER: 127.0.0.1
;; WHEN: Wed Jul 10 11:52:06 2019
;; MSG SIZE  rcvd: 43
```
Check `rcode` and `SERVER` in the output to confirm your configuration.

# DoT configuration
In the previous section unbound recursively resolved the DNS by querying the root servers.
But in most cases we would like to resolve them from a caching server close to us.
In this case we access the server using TLS like so,
```yaml
# DNSOverTLS addition to unbound.conf
server:
  tls-cert-bundle: /etc/ssl/certs/ca-certificates.crt # needed for verification of TLS

forward-zone:
  name: "."
  forward-tls-upstream: yes

  #nic.cz
  forward-addr: 193.17.47.1@853#odvr.nic.cz
  forward-addr: 185.43.135.1@853#odvr.nic.cz
  forward-addr: 2001:148f:ffff::1@853#odvr.nic.cz
  forward-addr: 2001:148f:fffe::1@853#odvr.nic.cz
```
As can be seen the format for `forward-addr` in the case of DoT involves specifying the IP, port and name.
The name is checked against the host mentioned in the TLS certificate securing our connection.
In this example I am using the host from (cz.nic)[https://www.nic.cz/odvr/], but other alternative exists from Cloudflare, Google and Quad9 to name a few.
As before, check the configuration using `drill` after restarting the service.
You can also check the syntax of the configuration file before restarting by using `unbound-checkconf` to prevent unnecessary round trips.
Hopefully by the end of this you have a secure and private DNS setup!
