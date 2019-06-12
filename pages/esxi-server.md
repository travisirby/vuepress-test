> _Learn how this leading hypervisor works on Packet bare metal._


### The Basics

Packet supports ESXi 6.5 as installable operating systems for most x86 based bare metal server configurations. With VMware ESXi, you can easily partition your server into virtual machines.

  

### Limitations of Support

Packet only supports ESXi in a stand-alone configuration. Running ESXi connected to a vCenter is not supported by Packet, but you can contact VMware directly for support with that configuration.

  

Packet Block Storage is not supported with ESXi. This is due to limitations of the underlying block storage system and compatibility issues with the ESXi Software iSCSI Adapter.

  

For networking configurations, only one vnic per Standard vSwitch is supported. Trunking of uplinks is not supported, as Packet’s network only support LACP for link aggregation and ESXi does not support that requirement. Attempting to add the second vnic to vSwitch0 will result in you being unable to access the server or VMs by any means other than the SOS Console.

  

### ESXi WebUI Access

By default our ESXi image injects a root password (which can be used for the ESXi Web Interface and the SOS Console login prompt) and this is visible in the portal for the first 24hrs after the server is deployed. After the first 24hrs, the root password will no longer be visible for security reasons, so please make a note of it or change it to one you know. In addition, any SSH keys you have associated with your project will be placed on the server during install. Your SSH key will work for the lifespan of the system, but be aware that injection process is one-time, so adding an SSH key via the portal or API will not add it to the host for the root account. This will need to be done manually by a user with an existing SSH key or via the SOS Console. 

  

### ESXi Root Lockout

In  ESXi 6.0 and above a security lockout feature was implemented on the root user for safety. After a number of failed login attempts, the server will trigger a lockout. This is a good safety measure for when you have public facing servers, or, even for internally exposed servers on your corporate network. We can’t always assume that it’s external bad actors who are the only ones attempting to breach your devices.

  

During provisioning, our platform injects SSH Keys to the device and disables password-based logins over SSH. This prevents lockouts caused by bad actors attempting to brute force SSH login attempts under normal situations. You can use those keys to reset the root lockout by running the command: 

`[root@esxi6:~] pam_tally2 --user root --reset`

  

The output would include number of attempts, the last attempt (date) & IP: 

`Login           Failures Latest failure     From`

`root                132  11/29/17           61.167.12.10`

`[root@esxi6:~]`

  

Once the reset has been completed, it's suggested to utilize ESXi[ firewall](https://www.altaro.com/vmware/managing-esxi-firewall/) to limit access to default SSH port 22 to only those authorized and/or alternate the default port to something specific for your use case. 

  

### ESXi Licensing

Packet does not offer licensing for ESXi at this time. By default, the OS comes with a 60-day evaluation license.  If you would like to utilize it beyond the 60-days you will need to acquire a license from VMware and activate it on your Packet machine(s) directly. 

  

### ESXi Networking

Every ESXi instance comes with a default /29 Public IP block where you have 4 usable IPs for your VMs.  From these 8 IPs:

  1st - network

  2nd - gateway

  3rd - ESXi management

  4th - available

  5th - available

  6th - available

  7th - available

  8th - broadcast

If you need more IPs, we also offer the ability to provision with a custom /28 Public IP block. With this bigger block, you will have 12 usable IPs. From the portal, when provisioning a new server, after selecting ESXi as the OS, you will see an additional option under “SSH & USer Data”.

  

Note! These additional IPs will get charged at the regular public IPv4 rates of $0.005/hr per IP

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/1718/1717020NCPXJGXWRCBGHPD0-1555029823334.png)

### 

### _Quirks of Standard vSwitch_

A Standard vSwitch within ESXi does not behave like a normal switch. The switches are auto-learning, like a normal ethernet switch, but any traffic that is not answered by a port group member is sent to the uplink port. This is a subtle but distinct difference that means in a default configuration, the “VM Network” on vSwitch0 is connected to the same public IP space as the ESXi host. Adding a new port group on vSwitch0 will not isolate the traffic from the Internet. Instead, if isolation is desired, a new Standard vSwitch should be created and a port group on that with no uplink vmnic attached. This will not leak traffic to the Internet. You can also add a vmnic to that vSwitch later to use the Packet L2 networking features described below.

  

### _Networking Between Hosts Using Layer 2_

The particular use case outlined here suggests that you have 2 ESXi hosts in the same datacenter. Using [our Layer 2 feature](https://support.packet.com/kb/articles/layer-2-overview) is a great way to connect virtual machines in ESXi together via a private network. To use this feature, you would need to create a VLAN in the portal and then convert the ESXi instances to “Hybrid Mode” networking. This will allow you to attach the VLAN(s) to the “eth1” interface. Then create a new vSwitch inside ESXi and attach the other vmnic interface that is marked as “UP” to that switch. By default, if only one VLAN is attached to the instance then the network traffic is not tagged. If there are two or more VLANs assigned to an instance, then all VLANs are tagged.

####   

### _Elastic IP Addresses_

By default, it is not possible to use Elastic IP addresses with ESXi. This is because of how we add addresses to your system by routing them to the first non-network IP in your block. By default, this is consumed by the ESXi vmkernel0 interface, and the network stack in ESXi does not support this configuration.

However, it is possible to manually re-configure ESXi through the WebUI or the SOS console to use the first available IP address and then deploy a Router VM on the old ESXi IP address instead. Example routers include (but are not limited to) vSRX, VyOS, and OPNsense. From there you can then either route the Elastic IP addresses to the or use our BGP capabilities. Here is how an example /29 network would look in that configuration:

  1st - network

  2nd - gateway

  3rd - router VM

  4th - ESXi Management

  5th - available

  6th - available

  7th - available

  8th - broadcast

  

### _vSwitch0 & Private Network Connectivity_

In the default setup, you should have two vmkernel interfaces configured with vSwitch0, one with your management IP address assigned during provisioning and one with the private network address also assigned during provisioning. Only one of the Physical NICs is connected to vSwitch0. Any traffic using an untagged port group on vSwitch0 can, thus. access the Public or Private IP network space assigned to your instance.

  

### _Precautions before using full L2 mode for networking_

By default, the ESXi instance has a second vmkernel interface installed for use with the Packet Private Network. This includes a route for 10.0.0.0/8 which will conflict if your L2 networks use any addresses in the same space. To prevent this conflict, login to the WebUI before converting the instance to L2 mode and remove the vmkernel1 interface and “Private Network” port group off of vSwitch0. That will also delete the route to avoid conflicts.

Once the instance is configured to use Full L2 Mode, you can only change the management interface using the SOS console. Note that the same rules of a single VLAN assigned to “bond0” in the portal mean it is untagged, and multiple VLANs assigned to “bond0” in the portal means that the management interface will need to be configured to use VLAN tagging for the desired VLAN.

  

### _Using a Private Network address instead of a public address for ESXi management_

You can use the Private Network address space assigned by Packet for your ESXi Management interface. Before making this change, it is important to delete the vmkernel1 interface and “Private Network” portgroup of vSwitch0 using either the SSH command line or via the WebUI. Once that is done, you can connect over the SOS Console and set the management address information to use the private IP space. Once that is done, access can be gained by using the “Customer (doorman) VPN” provided by packet. This provides an extra layer of protection preventing unwanted connection attempts to your ESXi WebUI and SSH interfaces. It also frees the Public IP address for use by a VM, like a Router VM.

  

---

###   

### ESXi Datastores

By default, the remaining space not consumed by the ESXi software on the boot drive for the instance is automatically assigned to a Datastore for ESXi called Datastore1. You can use the WebUI or esxcli command line tools from the SOS console or over SSH to create new datastores on any additional drives in your instance, or expand Datasrore1 to span multiple drives. Be aware that not all drives in the instance will be the same storage technology, so take care when expanding a datastore to ensure consistent performance.

####   

### _Packet Block Storage_

Packet Block Storage is not supported for use with ESXi, and it is not recommended to attempt using it.

  

### _Loading images for Virtual Machine_

Images can be loaded into the datastore(s) of ESXi using the WebUI, however, you will be limited to the upload speed of your connection and the speed of the WebUI of ESXi. The ESXi WebUI is also very latency sensitive, so uploading across continents and/or oceans can be very slow and unreliable. For better performance, you may wish to download images using the wget tool over SSH or the SOS Console instead. The version of wget included with ESXi does not support HTTPS, and most sites now silently redirect to HTTPS by default. For this reason, we offer a small selection of ISOs for download over HTTP at iso.packet.cloud for your convenience. The default datastore is located in /vmfs/volumes/datastore1 when connecting to the ESXi console over SSH or the SOS console. Downloading images does not work if the instance is running in full L2 mode. While the ISOs you may want are not available, you can launch a simple Linux VM from that selection, and use a browser from the Linux VM to download your desired ISO and upload it to the ESXi WebUI with better performance than attempting remotely.

  

###   

### External Resources for ESXi / VMware Users

[vSphere 6.x](https://www.vmware.com/support/pubs/vsphere-esxi-vcenter-server-6-pubs.html)

[ESXi 6.x](https://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.vsphere.doc/GUID-1B959D6B-41CA-4E23-A7DB-E9165D5A0E80.html)