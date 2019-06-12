> _Utilize tags to group your device(s)_

### **What is tagging?**

Tagging is a feature allowing you to apply custom labels to a single device or multiple devices. Device tagging is purely for filtering purposes at this time. 


#### How do I make use of the tagging feature? 

Tagging is available through our [API](https://www.packet.com/developers/api/devices/). An example of what this may look like:

     {        "plan": "baremetal\_1",        
              "operating\_system": "centos",        
              "facility": "ewr1",
             "tags": \["database", "primary"\],
             "locked": true
      }

The above payload would create a device in `EWR1`with CentOS as the specific OS and tag it with `database, primary` in addition it is set to lock the device to avoid it being deleted.

  

#### Can I edit tags? 

You can, it will take the following `PATCH`payload to accomplish that: 

  

 ````
 {        "hostname": "newhostname.server.com",
          "description": "Server description",
          "tags": \["new\_tag", "server"\],
          "billing\_cycle": "monthly",
          "locked": true,
      }

  ````

The above would add the tag `new_tag`and `server`in addition to that you also notice `[newhostname.server.com](//newhostname.server.com) `you can use the paylod to also change your hostname (the change will only be reflected via API calls and the portal). To change your hostname within the device it's self.