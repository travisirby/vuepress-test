[Proxmox](https://www.proxmox.com/en/) is a free ([community paid tier for support](https://www.proxmox.com/en/proxmox-mail-gateway/pricing)) hypervisor. While Proxmox is not an officially [supported Packet OS](https://support.packet.com/kb/articles/supported-operating-systems), this guide will help you get it up and running.

### Provision a Server with Debian 9

For this demonstration, we'll use Packet's [t1.small](https://www.packet.net/bare-metal/servers/t1-small/) bare metal configuration running Debian 9.  When deploying the server (which are available in Packet's core datacenters), please select Debian 9 and then add extra elastic IP's to the server using the custom subnet option.  Assign a [custom subnet size of /28](https://support.packet.com/kb/articles/custom-subnet-size) via the options slide out on the deploy screen:

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47019RWRDDBGNQTACPST0-1539927219799.png)



### Setting Up & Installing Proxmox

Once the device has provisioned and is reachable, login as "root" and update the Debian installation to the latest and greatest:

`apt-get update && apt-get upgrade -y`


To allow virtual machines access to the interwebs, a bridge must be created on the server.  The first step is to install the necessary bridge utilities:

`apt-get install bridge-utils`

  

The device hostname must include the 147.x IPv4 address in `/etc/hosts`

````
$ cat /etc/hosts
root@prox:~# cat /etc/hosts

147.75.75.82 prox prox.domain.com
````

# The following lines are desirable for IPv6 capable hosts

````
::1localhost ip6-localhost ip6-loopback

ff02::1ip6-allnodes

ff02::2ip6-allrouters

root@pve:~#
````
  

If your management IP (147.x) is missing, please add it as shown above. If it is present, you can verify it's inout correctly by running `hostname --ip-address` 

$ hostname --ip-address
147.75.75.162

  

To setup a bridge the following is my example. I moved `vmbr0`to the top and pushed `bond0`& `bond0:0` towards the bottom. Review & make changes/additions to your`/etc/network/interfaces. ` 

````
auto lo
iface lo inet loopback

auto vmbr0
iface vmbr0 inet static
    address 147.75.75.82
    netmask 255.255.255.240
    gateway 147.75.75.81
    dns-nameservers 147.75.207.207 147.75.207.208
    bridge\_ports bond0
    bridge\_stp off
    bridge\_fd 9
    bridge\_hello 2
    bridge\_maxage 12

iface vmbr0 inet6 static
    address 2604:1380:1:7200::1
    netmask 127
    gateway 2604:1380:1:7200::

auto vmbr0:0
iface vmbr0:0 inet static
    address 10.99.207.1
    netmask 255.255.255.254
    post-up route add -net 10.0.0.0/8 gw 10.99.207.0
    post-down route del -net 10.0.0.0/8 gw 10.99.207.0

auto bond0
iface bond0 inet manual
    bond-downdelay 200
    bond-miimon 100
    bond-mode 5
    bond-updelay 200
    bond-xmit\_hash\_policy layer3+4
    bond-slaves enp0s20f0 enp0s20f1
`````
️

_Please Note: the IPs above are examples. Ensure you are using your management (147.x), IPv6, and private network IPs as shown in your network interfaces file._

Reboot the device.  After the reboot, you should find ip configuration on `vmbr0`and the output of the bridge configuration `brctl show`

````
user@pve$  brctl show
bridge namebridge idSTP enabledinterfaces
vmbr08000.0cc47ab58b16nobond0
user@pve$
````
  

Next, create the repo necessary to download packages for Proxmox

````
echo "deb http://download.proxmox.com/debian/pve stretch pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
````
  

To ensure the repo is the validated one, it is necessary to install the key: 

````
wget http://download.proxmox.com/debian/proxmox-ve-release-5.x.gpg -O /etc/apt/trusted.gpg.d/proxmox-ve-release-5.x.gpg
`````
  

Update to include the new repository :

`apt update `

  

Install the Proxmox packages: 

`apt install proxmox-ve postfix open-iscsi`

  

### Cleanup and Final Reboot

Configure postfix according to your network. If you have a mail server in your network, you should configure postfix as a **satellite system**, and your existing mail server will be the 'relay host' which will route the emails send by the proxmox server to the end recipient. If you don't know what to enter here, choose **local only**.

When installation has completed, reboot the server, after which you should be able to access the Proxmox UI via url: [http://your.device.ip:8006](http://your.device.ip:8006/).  Ta da! 

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47016TZNMNWSCJSTZNDJ0-1539927217523.png)


### Creating an alternate user

By default the root user is restricted by accessing the device via specific SSH keys utilized during provisioning. Password authentication is also disabled. It's suggested to create a wheel user (other than root) to access not only the UI of Proxmox but also the command line. 

  

#### Create admin group

pveum groupadd admin -comment "System Administrators"

  
#### Add necessary permission

`pveum aclmod / -group admin -role Administrator`


#### Add wheel user to group

`pveum usermod yourwheelusernamehere@pam -group admin`


From this point on, the newly created wheel user will have access to the UI. 


#### 403 Unauthorized

This error is common, it suggests your device has not yet been setup for a [paid subscription to the enterprise repo](https://www.proxmox.com/en/proxmox-ve/pricing). You are welcome to remit payment to obtain this access, or you can remove the enterprise repo & then setup the repo to pull from the non-subscription repo. 

`rm /etc/apt/sources.list.d/pve-enterprise.list`

  

````
echo "deb http://download.proxmox.com/debian/pve stretch pve-no-subscription" > /etc/apt/sources.list.d/pve.list
````
  

Once  updated, run `apt update` without the resulting error.


### Creating your first VM / Container

Now comes the fun part, creating your first container or virtual machine. 

### **Download / Upload Container Template**

Proxmox comes with ready to download/install container templates, to obtain a list you can do this via UI 

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47018YDRWQRGHYRNQMRK0-1539927218884.png)

  

Once Templates is clicked a window will appear, with the ever growing list of available templates. 

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47020SGTWDRCSYDJXASC0-1539927222864.png)

  

If you have you own, you are welcome to upload through the UI as well. Once you have found the template you require or uploaded your own, click the Create CT and process with the necessary steps to complete your first container. 

  
![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47017WDZPKZAMMKYHCRA0-1539927218116.png)

  

To create [VM](https://pve.proxmox.com/wiki/Qemu/KVM_Virtual_Machines), please see their [documentation](https://pve.proxmox.com/wiki/VM_Templates_and_Clones) surround that. To use our block storage with your Proxmox Installation, please see [here](https://support.packet.com/kb/articles/proxmox-followup-block-storage).