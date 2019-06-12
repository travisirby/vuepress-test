> _A quick guide to Packet's powerful API, as well as common DevOps tools and integrations._

  

Packet was built for a developer-centric world, which means that our API is the heart and soul of the platform. In fact, the Packet portal is just a consumer of our API - so anything you can do in the portal you can also do via the API! (protip: “undocumented” api calls can be found and used by using your browser’s inspection console)  
  
Users can also leverage a wide range of integrations and tooling - some that we’ve produced (like our Docker Machine driver and various API libraries) and others that are supported by their respective communities (like Docker Cloud, Terraform, or Ansible).  
  
Interacting with Packet bare metal is a lot like dealing with VM’s at a public cloud.  So if you are comfortable with AWS or Digital Ocean then working with Packet should feel familiar.


### **Prerequisites**

  

The first thing you’ll want to do once you’ve signed up and added your ssh key, is to grab an API key. Your API keys operate with all the permissions your user account in the portal does, so be sure to keep them safe. Name your keys something that will remind you where you are using them, e.g. the name of a workstation or application.

###   

###   

### Project Level API Key

  

Limit API access  to a particular project. To create one, go to your specific project, and then project settings as shown below.

  

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/35/34143SHJQYJNWKMTTNBC0-1539752429811.png)

###   

###   

### User Level API Key

  

User level API permits complete access to all projects/ORGs assigned to the particular user. To create one, click user icon top right then click API Keys

  

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/35/34142KYZTDPPKSZGZPQX0-1539752234437.png)

###   

### Obtain project ID

  

The next thing that you need to know is that most integrations require a project ID, so once you’ve created your first project, navigate to the settings area for that project to find the project ID.

  

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/35/34141WPZCJCDYMXTRXRN0-1539751933672.png)

  
### **Consuming the API**

Everything that you with Packet, you can do via our API, which is modern, RESTful and easy to use! 

  

1.  **API Docs** - You can check out our [API documentation here](https://www.packet.com/developers/api/).
2.  **API Libraries** - You can find the [full list of official API clients here](https://www.packet.com/developers/integrations/).
3.  **DevOps Tools** - We integrate with a [broad array of popular tools](https://www.packet.com/developers/integrations/).
4.  **CLI** - Interact with our API through our [official command line interface](https://github.com/packethost/packet-cli).

  

No matter how you're interacting with our API, there are a few things to point out that will help you get started on the right foot:

  

*   Servers are called "Plans" in the API, and are specified with slugs that start with “baremetal”, e.g. “c1.small.x86” represents our c1.small server. You can get the full list of available plans by calling the [plans method](https://www.packet.com/developers/api/devices/#devices-plans).
*   Our Block Storage service is managed in the block-storage name space, the actual [block storage instances](https://www.packet.com/developers/api/volumes/) are referred to as "volumes" there.
*   IP's are managed in a few places in the API. You can view the list of them in the projects namespace and you can assign/unassigned them to devices in the devices namespace.
*   You can also query the [capacity endpoint](https://www.packet.com/developers/api/capacity/#capacity-capacity) to get a status on availability of server types in various facilities.

### **Orchestration Tools**
A lot of users interact with our platform through tools like Ansible, Terraform or Libcloud.  While each of these tools have their own special approach, they essentially help you manage infrastructure across environments and cloud providers.

Packet has helped write drivers (often called 'providers') for these tools, which means there is native support available. A good example is Terraform, [which is described nicely here](https://www.terraform.io/docs/providers/packet/).

  

Our full list of orchestration tools is [available here](https://www.packet.com/developers/integrations/).

### **External Platforms**

  

This is the easiest of them all! 

  

A number of platforms, such as Docker Cloud, Cloud66, Containership, and StackpointCloud have directly integrated with the Packet API.  This means you simply need to have an active Packet account and an API key to get started. Grab your API key from the Packet portal, provide it in the portal of the integration you’re working with, and you should be off to the races.
