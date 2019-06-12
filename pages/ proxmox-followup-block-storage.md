Now that you have successfully converted Debian 9 to Proxmox, this guide will assist you in adding additional storage space. Having this storage will assist in HA & live migrations, should you have a cluster setup. 

  

---

### Creating & Attaching Storage Volume

Creating a storage volume is done within the project where the Proxmox device resides. You can find that at the top of the portal. 

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47029QZZHJXDYYBWDMGZ0-1539929297191.png)

  

When creating a volume you have the choice between standard or performance:  
  
**Standard: \[**$ 0.000104/GB per hour\]

*   500 IOPS per volume. 
*   Good for backups and general use cases

**Performance:  \[**$ 0.000223/GB per hour\]

*   15,000 IOPS per volume
*   Excellent for I/O heavy workloads

  

With the volume created you will need to manage the device to connect the volume  to the appropriate device. 

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47021PSRPZXNKPZMAXZA0-1539929293597.png)![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47027BTCRNZXPXZQBCPZ0-1539929295463.png)![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47028KCZDCWYQTBYRQBK0-1539929296673.png)

  

After selecting the device and saving the volume attachment, you must run the `packet-block-storage-attach` script CLI (command line, within the device). The command must be run as root user or a wheel user with sudo access.

$ packet-block-storage-attach
portal: 10.144.34.101 iqn: iqn.2013-05.com.daterainc:tc:01:sn:71212b32f5061ea7
portal: 10.144.50.129 iqn: iqn.2013-05.com.daterainc:tc:01:sn:71212b32f5061ea7
Jul 06 23:02:58 | mpatha: ignoring map
create: volume-8bc499e7 (360014054a502ec4a48c46aebc1f74a0d) undef DATERA,IBLOCK
size=100G features='0' hwhandler='1 alua' wp=undef
\`-+- policy='round-robin 0' prio=50 status=undef
  |- 2:0:0:0 sdb 8:16 undef ready running
  \`- 3:0:0:0 sdc 8:32 undef ready running
Block device /dev/mapper/volume-8bc499e7 is available for use

  
   ️_Please Note: Multipath is not enabled by default on Proxmox, please see _[_this guide_](https://pve.proxmox.com/wiki/ISCSI_Multipath)_ to set that up. _

  

---

### Adding Storage through Proxmox UI

Adding the newly created storage volume to the Proxmox install is done in three steps: Datacenter → Storage → iSCSI

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47030SAHCJRBMAMCJDBW0-1539929299085.png)

  

Continue the process on the popup window requesting information about the volume you are attaching: 

*   ID: the name for your storage mount point
*   Portal: the IQN which is the 10.x IP address from the `packet-block-storage-attach` command you previously ran. 
*   Target: once a valid 10.x IP address is adjusted you should see see a target in which you can select. 

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47023TMRYKJBMBQSTCMM0-1539929295681.png)

  

Upon completion your storage volume appears on the top under the primary storage screen 

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47026QHYZJYYRPJAGGNQ0-1539929295070.png)

  

To utilize the newly added block storage volume, you must create another storage mount point, this time LVM. 

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47025HGGJYCKZJACHNTB0-1539929294281.png)

  

The next screen prompts you for items required to complete this storage LVM. 

*   ID: a name you wish to call your this volume, it will contain the virtual machine disks & container images. 
*   Base Storage: is the iSCSI you previously added 
*   Base Volume: is as shown, and the only option
*   Volume Group: a name in which you want to call the storage group. 
*   Content: You can have one or both, VM disk images / container images.

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47024AYJAYMQHCSHJHBA0-1539929296115.png)

You should now see both the iSCSI and LVM listed under storage

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/48/47022DWKPBXTDAPTSAAX0-1539929295930.png)

  

Bravo! You now have a working block storage volume to expand your existing space for your containers / virtual machines.