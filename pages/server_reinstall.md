> _Redeploy an instance while keeping the same IP addresses._

* * *

### **What is server reinstall & how is it helpful?**

  

The Server Reinstall feature enables you to provision a new instance on the same hardware, which is perfect if you want to keep the assigned IP addresses, userdata, etc.  

Reinstalls take a little longer than a normal fresh provision, since the server has to go through a deprovision process first. During deprov, we bring the server back to a “ready state”, which involves wiping the disks, checking firmware, and other elements. As such, **a reinstall will fully wipe any data on the server**. After the the deprovision is complete, we’ll install the OS again through a normal provision, but using the same hardware and IP addresses assigned previously. 

Any Block storage volume needs to be properly detached, in order for it not to be impacted, but you will need to re-attach it once your server is re-installed.

  

===

### **Usage**

  

Portal
------

In our Portal, the Reinstall option can be found on the Server’s detail page, under Server Actions. After you select the option, you’ll be asked to confirm your intentions - better safe than sorry!

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/42/41926HBTGKZCWSWBQZDN0-1539827161011.png)

  

  

---

### API

From the API, the reinstall action can be found under the /devices/{id}/actions endpoint.

Here is a simple API call:

````
curl -X POST -H 'X-Auth-Token: {token}' -H 'Content-Type: application/json' "https://api.packet.net/devices/{server\_uuid}/actions" -d '{"type":"reinstall"}'
````