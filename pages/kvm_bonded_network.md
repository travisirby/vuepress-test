### What is a linux bridge?

A bridge is a way to connect two [Ethernet](http://en.wikipedia.com/wiki/Ethernet) segments together in a protocol independent way. Packets are forwarded based on Ethernet address, rather than IP address (like a router). Since forwarding is done at Layer 2, all protocols can go transparently through a bridge.

  

===

### What is qemu?

**QEMU** (short for **Quick Emulator**) is a free and open-source hosted hypervisor that performs hardware virtualization (not to be confused with hardware-assisted virtualization).

  

===

### What software is required to setup a bridge?

*   CentOS/Redhat: yum install bridge-utils 
*   Debian/Ubuntu: apt-get install bridge-utils 

  

===

### How to configure a bridge on a bonded network?

**_Note:_** This example, will make use of a [custom subnet](https://support.packet.com/kb/articles/custom-subnet-size) of /29. 

Adding the basic bridge configuration to the existing network configuration (Debian/Ubuntu) example below, you will see `vmbr0`has been added merely as a placeholder

\# nano /etc/network/interfaces

auto lo
iface lo inet loopback

auto bond0
iface bond0 inet static
    address 147.75.96.218
    netmask 255.255.255.248
    gateway 147.75.96.217
    bond-downdelay 200
    bond-miimon 100
    bond-mode 4
    bond-updelay 200
    bond-xmit\_hash\_policy layer3+4
    bond-lacp-rate 1
    bond-slaves enp2s0 enp2s0d1
    dns-nameservers 147.75.207.207 147.75.207.208
iface bond0 inet6 static
    address 2604:1380:0:6900::5
    netmask 127
    gateway 2604:1380:0:6900::4

auto bond0:0
iface bond0:0 inet static
    address 10.99.54.5
    netmask 255.255.255.254
    post-up route add -net 10.0.0.0/8 gw 10.99.54.4
    post-down route del -net 10.0.0.0/8 gw 10.99.54.4

auto enp2s0
iface enp2s0 inet manual
    bond-master bond0

auto enp2s0d1
iface enp2s0d1 inet manual
    pre-up sleep 4
    bond-master bond0

auto vmbr0
iface vmbr0 inet static
    bridge\_ports bond0
    bridge\_stp off
    bridge\_fd 0

  

With the above bridge place holder included in the bonded network configuration above & the use of a custom subnet of `/29` we migrate the details of `bond0`  (IP etc) to `vmbr0` while keeping everything else in place, see the following example:

auto bond0
iface bond0 inet manual
    bond-downdelay 200
    bond-miimon 100
    bond-mode 4
    bond-updelay 200
    bond-xmit\_hash\_policy layer3+4
    bond-lacp-rate 1
    bond-slaves enp2s0 enp2s0d1

auto vmbr0
iface vmbr0 inet static
    address 147.75.96.218
    netmask 255.255.255.248
    gateway 147.75.96.217
    bridge\_ports bond0
    bridge\_stp off
    bridge\_fd 0
    dns-nameservers 147.75.207.207 147.75.207.208

️ 

Please Note: The bridge will only function with a [custom subnet](https://support.packet.com/kb/articles/custom-subnet-size) size of /29 & /28. For elastic IP's, scroll to the bottom.

  

===

### How to configure a bridge on a non-bonded network?

iface eth0 inet manual

auto br0
iface br0 inet static
    address  147.75.88.218
    netmask  255.255.255.248
    gateway  147.75.88.217
    bridge\_ports eth0
    bridge\_stp off
    bridge\_fd 0
    bridge\_maxwait 0

  

The above configuration is more for our [Layer2 ](https://support.packet.com/kb//articles/layer-2-overview)with the default bond broken. 

  

===

### How to configure a bridge with Elastic IP's?

Since elastic IP's are routed directly to your machine, they don't need a bridge\_port towards your bond interface.  
  
   Adding the following configuration to your /etc/network/interfaces file will work to create a bridge for KVM using Elastic IP's.  
  
   In this example, we will configure the following elastic IPv4 subnet.

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/47/46251NKYZJXMSHXXSHQH0-1539911249081.png)

  

auto vmbr1
iface vmbr1 inet static
    address 147.75.97.113
    netmask 255.255.255.248
    network 147.75.97.112
    broadcast 147.75.97.119
    bridge\_ports none
    bridge\_stp on
    bridge\_fd 0
    bridge\_maxwait 0

VM's using the above Elastic IP's will need the following configuration:

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/47/46252YQXPKSZYWCBDHCA0-1539911249939.png)

  

You can run the following command to bring the bridge up, you don't need to restart the bond0 interface since there is no changes made to that interface.

ifup vmbr1

  

===

### Restart Network

Once the interfaces file has been configured you can restart the server, or use`ifdown -a && ifup -a` to restart the network in one command.

If the network fails to come back up, you can log in via SOS console and revert the changes made to restore the network.

  

===

### Check Bridges

In this example, vmbr0 is the original static bridge on a bonded network, while vmbr1 is the elastic IP bridge which requires no interface attachment. 

\# brctl show
bridge name     bridge id               STP enabled     interfaces
virbr0          8000.525400ffa2f0       yes             virbr0-nic
vmbr0           8000.fe00934b6172       yes             bond0
vmbr1           8000.fe0400ffa272       yes 

  

### External Resources

*   [Bridge Network Connections (Debian)](https://wiki.debian.org/BridgeNetworkConnections)
*   [Setup KVM & Bridge (Ubuntu)](https://www.cyberciti.biz/faq/installing-kvm-on-ubuntu-16-04-lts-server/)
*   [Bridge Setup (CentOS/RedHat)](http://www.itzgeek.com/how-tos/mini-howtos/create-a-network-bridge-on-centos-7-rhel-7.html)