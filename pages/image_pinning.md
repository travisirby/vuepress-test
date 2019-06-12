> _How to pin a custom operating system image at Packet._

#### What is Image pinning?

Image pinning is the ability to call a particular release version of a Packet official image, or a custom image utilizing its image tag.


#### How do I find the image tag?

Current device utilizing either an official Packet image, or custom image, you can query against the metadata service from within the device: 

 ```
 $ curl -s https://metadata.packet.net/metadata | jq '.operating\_system.image\_tag'
```

The following would be the output: 

```
"69318b5dab479adf0009c4c4ed42b205250954a8"
```

Or, if you want to travel back to a previously released version of an official Packet image, or custom image, you can obtain the image tag from our repo [here](https://app.intercom.io/). Or, your particular repo on GitHub/Lab.

#### Creating device with image tag via API

```
curl -s -H 'Content-Type: application/json' -H 'X-Auth-Token: '<API-Key>'' -d '

{ 

   "batches":[

        { 

           "hostname":"test.image.com", 

           "description":"This is my image test server",

            "plan":"<server-type>", 

           "operating_system":"ubuntu_16_04-baremetal_1", 

           "userdata":"#cloud-config\n#image_repo=https://somegitserver/user/image-repo.git\n#image_tag=<commit sha>", 

           "facility":"ewr1",

            "billing_cycle":"hourly", 

           "quantity":'<how-many>' 

       } 

   ]

}' -X POST https://api.packet.net/projects/<PROJECT UUID>/devices/batch


````



Passing image\_tag through the API will pin a specific image - either an official one that Packet maintains, or a custom image that you maintain.  

  

===

### **Creating device with image tag via Portal**

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/42/41952HXDKZHRSYHZHKHC0-1539828259708.png)

Prior to deploying your device, click 'SSH & User Data'. 

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/42/41953BPZWHTMQKPGMSKZ0-1539828260699.png)

Under 'Add optional user data' the following would be required to deploy your particular version: 

```
#cloud-config #image\_repo=https://somegitserver/user/image-repo.git #image\_tag=b1ce89c2584b9fe1513ec72a481fd1c662db6808
```