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
`/etc/dhclient.conf`:

```
interface "igb0.832" {
    vlan-id 832;
    # DHCP Protocol Timing Values
    timeout 60;
    retry 15;
    reboot 0;
    select-timeout 0;
    initial-interval 1;
    send dhcp-class-identifier "sagem";
    send user-class "+FSVDSL_livebox.Internet.softathome.Livebox4";
    send option-90 00:00:00:00:00:00:00:00:00:00:00:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX;
}
```

Le client dhcp freebsd est buge:

Il faut donc recompiler le client dhcp avec le patch suivant:

checkout les sources:
`svn checkout https://svn.freebsd.org/base/releng/11.1 /usr/src`


Patcher le client dhcp:

```diff
--- tables.c    2017-07-29 16:53:19.483075000 +0200
+++ tables.c    2017-07-29 16:54:12.534537000 +0200
@@ -365,6 +365,7 @@
        DHO_INTERFACE_MTU,
        DHO_ALL_SUBNETS_LOCAL,
        DHO_BROADCAST_ADDRESS,
+       DHO_DHCP_USER_CLASS_ID,
        DHO_PERFORM_MASK_DISCOVERY,
        DHO_MASK_SUPPLIER,
        DHO_ROUTER_DISCOVERY,
```
`cd /usr/src/sbin/dhclient/`

`patch < orange.patch`

`make`

`cp dhclient /sbin`

##### NAT/FW

To be improved.


`/etc/pf.conf`


```
#interfaces
ext_if="em0.832"
tv_if="em0.840"
int_if="em1"

#options
table <lan_v6> persist
set skip on lo
scrub in

#nat
nat on $ext_if inet from 192.168.1.0/24 -> ($ext_if)

#filters
block log
pass keep state
pass quick proto ipv6-icmp from any to any
pass in log quick proto tcp from xxx.xxx.xxx.xxx to any port ssh flags S/SA keep state
pass out quick on $ext_if inet from 192.168.1.0/24 keep state

#ip6 
pass out quick on $ext_if inet6 keep state
pass in quick on $ext_if inet6 from fe80::ba0:bab keep state
```

##### Configuration TV

##### Configuration ip6

https://github.com/tomaszmrugalski/dibbler


TBD
