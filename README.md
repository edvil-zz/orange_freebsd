### Routeur freebsd avec Orange Fibre.

#### Hardware

Un pc avec au minimum 2 interfaces reseaux.

http://www.qotom.net/

#### Software

Freebsd: http://www.freebsd.org

#### Configuration

igb0: interface connectee a l'ONT orange
igb1: interface LAN

##### Configuration ipv4

`/etc/rc.conf`:

```
ifconfig_igb0="up"
vlans_igb0="832 838 840"
ifconfig_igb0_832="DHCP vlanpcp 0"
ifconfig_igb0_838="DHCP vlanpcp 4"
ifconfig_igb0_840="inet 192.168.255.254 netmask 255.255.255.255 vlanpcp 5"
ifconfig_igb1="inet 192.168.1.1 netmask 255.255.255.0"
```

##### NAT/FW

##### Configuration TV

