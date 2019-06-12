```
lspci | egrep -i --color 'network|ethernet'
````
````
root@x1:~# lspci | egrep -i --color 'network|ethernet'
02:00.0 Ethernet controller: Intel Corporation Ethernet Controller X710 for 10GbE backplane (rev 02)
02:00.1 Ethernet controller: Intel Corporation Ethernet Controller X710 for 10GbE backplane (rev 02)
root@x1:~#
````
````
auto lo
iface lo inet loopback

auto bond0
iface bond0 inet static
    address 147.75.48.227
    netmask 255.255.255.254
    gateway 147.75.48.226
    bond-downdelay 200
    bond-miimon 100
    bond-mode 5
    bond-updelay 200
    bond-xmit_hash_policy layer3+4
    bond-slaves eno1
    dns-nameservers 147.75.207.207 147.75.207.208
iface bond0 inet6 static
    address 2604:1380:4070:1600::1
    netmask 127
    gateway 2604:1380:4070:1600::

auto bond0:0
iface bond0:0 inet static
    address 10.48.11.1
    netmask 255.255.255.254
    post-up route add -net 10.0.0.0/8 gw 10.48.11.0
    post-down route del -net 10.0.0.0/8 gw 10.48.11.0

auto eno1
iface eno1 inet manual
    bond-master bond0
    ````
    