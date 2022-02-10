This is the a-vpn wireguard configuration:

```
[Interface]
Address = 10.1.253.1/32
ListenPort = 51194
PrivateKey = <a-vpn private key>
[Peer]
PublicKey = <b-vpn public key>
AllowedIPs = 10.6.253.1/32, 10.6.254.0/24, fd06::/64
```

This is the b-vpn wireguard configuration:

```
[Interface]
PrivateKey = <b-vpn private-key>
Address = 10.6.253.1/32
[Peer]
PublicKey = <b-vpn public key>
AllowedIPs = 10.1.253.1/32, 10.1.254.0/24, fd01::/64
Endpoint = a-vpn:51194
PersistentKeepalive = 20
```

a-vpn dnsmasq.conf:
```
bind-interfaces
# These not necessary becauase it ignores it anyway. these are upstream servers, so 8.8.8.8
#server=fd04::1
#server=10.4.0.1
log-queries
log-dhcp
dhcp-range=10.1.254.2,10.1.254.254,255.255.255.0,12h
interface=ve-a-net
domain=a.gx
expand-hosts
address=/a.gx/10.1.254.1
dhcp-range=fd01::2, fd01::500, ra-names, slaac, 12h
# IPv4 DNS Server
dhcp-option=option:dns-server,10.1.254.1
# IPv4 gateway
dhcp-option=option:router,10.1.254.1
# IPv6 DNS server
dhcp-option=option6:dns-server,[fd01::1]
# This speeds up lease acquisition
dhcp-authoritative
dhcp-host=9a:8e:17:b7:70:7a,10.1.254.2
```

b-vpn dnsmasq.conf:
```
bind-interfaces
log-queries
log-dhcp
dhcp-range=10.6.254.2,10.6.254.254,255.255.255.0,12h
dhcp-range=fd06::2, fd06::500, ra-names, slaac, 12h
dhcp-option=option6:dns-server,[fd01::1]
interface=ve-s-net
domain=s.gx
expand-hosts
address=/s.gx/10.6.254.1
dhcp-option=option:dns-server,10.6.254.1
dhcp-option=option:router,10.6.254.1
dhcp-authoritative
dhcp-host=02:cb:e6:3d:0b:5f,10.6.254.2
```

After the wireguard interface is up, the routes will be auto-added on each side due to them being present in AllowedIPs.
This causes the IPs to be reachable fom both sides. 
 