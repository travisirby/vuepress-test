> _Details about deploying Windows Server & Hyper-V on Packet._

* * *

Packet currently offers Windows Server 2012 R2 and Windows Server 2016. A comparison grid of our supported Operating Systems, including licensed OS's, can be found here.

Windows 2012 R2 is supported on the following x86 server configurations: 

*   t1.small 
*   c1.small
*   x1.small
*   m1.xlarge
*   c1.xlarge
*   m2.xlarge

Windows 2016 Standard is supported on the following x86 server configurations: 

*   t1.small 
*   c1.small
*   x1.small
*   c2.medium
*   s1.large
*   m1.xlarge
*   c1.xlarge
*   m2.xlarge



### Windows 2012 & 2016 License Pricing:

Packet charges a flat $0.01 / hr per core for both the 2012 R2 and 2016 Standard versions of Windows Server.  Fees will be added to your invoice on the 1st of each month.  Since various server configurations have a different number of cores based upon their processor(s) - as such the additional licensing costs will vary. 

### How do you access RDP (Remote Desktop)?

Once the server you deployed is Active, you can RDP in as **Admin** and using the password you will find on the server's details page.  
  
Our Windows image utilizes the default RDP port 3389. To create an RDP session, dependent upon your local OS here are a few options: 

*   Windows Machine:  Windows → All Programs →  Remote Desktop Connection
*   OS X/MacOS there many applications for RDP sessions for simplicity sake we suggest Microsoft Remote Desktop client. You can find this through the App Store, or by clicking [here](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12). 
*   Linux like other OS's has many applications for RDP sessions, one that would work in this case would be [Remmina](https://www.remmina.org/wp/). You can learn more about it via their [FAQ](http://www.remmina.org/wp/faq/) & see screenshots of it in action [here](http://www.remmina.org/wp/screenshots/). 


### What is Hyper-V?

Hyper-V is a hybrid hypervisor, which is installed from OS (via [Windows wizard of adding roles](https://technet.microsoft.com/en-us/library/hh846766(v=ws.11).aspx#BKMK_Step1)). However, during installation, it redesigns the OS architecture and becomes just like a next layer on the physical hardware (refer to pic.1).

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/47/46840ARBXNGNXHGNYXJC0-1539920217670.png)

  

### How do you install Hyper-V?

It's simple and the same as any typical program installation. You can activate the Hyper-V role through the Server Manager, and then perform the installation by following the wizard. 

The following screenshots are what you during the activation/installation of Hyper-V.

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/47/46851MNXQZSGKHZANYZH0-1539920218953.png)![](https://deskpro-cloud.s3.amazonaws.com/files/26944/47/46852SHTQXCAZATXPCHY0-1539920219343.png)![](https://deskpro-cloud.s3.amazonaws.com/files/26944/47/46854ADMWGZZPCNGJRWM0-1539920221530.png)![](https://deskpro-cloud.s3.amazonaws.com/files/26944/47/46853HZBTHZRXQSCDZMG0-1539920222871.png)![](https://deskpro-cloud.s3.amazonaws.com/files/26944/47/46843CQGDBCRWNTAYAHQ0-1539920218521.png)![](https://deskpro-cloud.s3.amazonaws.com/files/26944/47/46855CRBQJTSRNANAZYQ0-1539920223930.png)

### **Additional Resources:**

*   [Create your first VM](https://technet.microsoft.com/en-us/library/hh846766(v=ws.11).aspx#BKMK_Step2)
*   [Hyper-V Community](https://social.technet.microsoft.com/Forums/windows/en-US/home?forum=winserverhyperv)
*   [Hyper-V Documentation](https://app.intercom.io/)
*   [Packet Network FAQ](https://app.intercom.io/)