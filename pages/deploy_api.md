> _Common methods to interact with infrastructure the 'cURL' way._

* * *

It is useful to try out different API calls before you incorporate them into your code. In fact, some information is only available via our API, and it is convenient to be able to review this information at the command line before provisioning, or setting up a Terraform script.

Luckily we can use a few simple cURL commands to do just about everything within our API.

  

### **Methods**

  

First things first, we need to specify which HTTP method we wish to request. This will vary depending on the API call, but GET, POST, PUT, and DELETE are all possible, so we use the -X option to specify which method we want to use.

  

### **Header Information**

  

In addition to the request type we need to pass along our API key. We can do this with the customer header option, -H, followed by the the header ‘X-Auth-Token: <your-API-key>’. Below is an example of an API call to return a list of inventory availability. With the POST and PUT methods you will need to specify the content type in the header as well with ‘Content-Type: application/json’.

  

### **A Simple GET Request**

  
````
~$ curl -X GET -H ‘X-Auth-Token: <API-key>’ ‘https://api.packet.net/capacity 
````

You may notice that the output of this call is a bit messy, to clean things up a little we can pipe the output to jq.  
  

**Note:** you may need to install JQ first. `~$ sudo apt install jq`   
  
```
~$ curl -X GET -H ‘X-Auth-Token: <API-key>’ ‘https://api.packet.net/capacity’ | jq ‘.’
````


This should make things a bit more human readable by applying the appropriate json formating to the API call’s output.|  
  

### **A Simple POST**

  

Let us look at a simple example of POST using the content type header. For this we will use the same API call (capacity) which will return some information about inventory. Except this time we wish to know about a specific inventory range, for a specific server, in a specific datacenter. For example, say we want to know if there are between 1 and 100 t1.small servers in Tokyo.

```
curl -X POST -H "X-Auth-Token: <API-KEY" -H "Content-Type: application/json" -d '  
{"servers": [  
 {  
 "facility": "nrt1",  
 "plan": "t1.small.x86",  
 "quantity": "1"  
 },  
 {  
 "facility": "nrt1",  
 "plan": "t1.small.x86",  
 "quantity": "100"  
 }  
]}' "https://api.packet.net/capacity" | jq '.'`
````

**NOTE**: If everything returns true no output will be printed to the terminal.

#### Provisioning Devices

As you might expect, POST is the method used when provisioning new devices. The structure is similar to the post request we just saw, once again specifying json application as our content type, and using the -d parameter to specify the data we wish to POST. The body of our POST should indicate the device configuration. There are many possibilities here, and some may depend on the server type and operating system. For more information on constructing the body of the devices request see our complete [API documentation](https://www.packet.net/developers/api/devices/). Here is an example of a simple POST request to provision an Ubuntu type\_0 server in EWR1:

````
curl -v -X POST -H 'X-Auth-Token: <API-KEY>' -H 'Content-Type: application/json' -d '  
{  
 "facility": "ewr1",  
 "plan": "t1.small.x86",  
 "hostname": "string",  
 "description": "string",  
 "billing_cycle": "hourly",  
 "operating_system": "ubuntu_16_04",  
 "userdata": "string",  
 "locked": "false"  
}' "https://api.packet.net/projects/<Project-ID>/devices"` 
````

**Note:** Here I have chosen not to attach an SSH key to the server. This means I would need to upload one manually later after the server has been provisioned. However, you may choose to associate an existing project or personal SSH key by adding the  
  

````
"project_ssh_keys": [  
"string"  
],` ```or ``` `"user_ssh_keys": [  
"string"  
],` 
````
parameter to your request body. The string here should be either the SSH key ID of your project key, or the user ID of the personal SSH key holder, both can be obtained by API, so let’s take a look at those calls.

  

### **SSH Keys**

  

Similarly to deploying hardware, the Packet API can be used to upload, update, retrieve, and delete SSH key with GET, POST, PUT and DELETE. The cURL requests are pretty straight forward here. In the above example we needed the SSH key ID in order to associate one of our project keys to our newly deployed server. Let’s look at the how we can get that.

````
curl -X GET -H 'X-Auth-Token: <API-KEY>' "https://api.packet.net/projects/<project-ID>/ssh-keys" | jq '.ssh\_keys\[\] | .label, .id'
````

**Note**: unfiltered this request would return the key as well.

### **Users**

As stated earlier, provisioning with a personal SSH key requires the user’s ID as the string value to the “user\_ssh\_keys” parameter. This can also be obtained with a simple API call using the GET method, but be aware that there are 2 possible calls, and the difference is subtle.

1.  User (only you)If you wish to retrieve only your personal user information you can leverage the “user” call (example below).  
      
    `` `~$ curl -X GET -H ‘X-Auth-Token: <API-KEY>’ “https://api.packet.net/user”` ``     Again, there is a lot of information here, and a simple jq filter, such as ~$jq ‘.id’ can save some time if all you want is the user ID.
2.  Users (collaborators included)If you need to retrieve the user information of one of your collaborators you can utilize the “users” call, which will return the user information of all collaborators in your project.  
      
    `` `~$ curl -X GET -H ‘X-Auth-Token: <API-KEY>’ “https://api.packet.net/user”` ``
3.  

This can return quite a bit of information, so filtering for username and ID may be in order: ~$jq ‘.users\[\] | .full\_name, .id’.

  

===

### **Managing Instances**

  

Now that your server is up and running, let’s look at some POST methods to help you manage things.

First let’s see how we can change the various server states with POST. The example below reboots the server specified by its device ID.

~$ curl -X POST -H ‘X-Auth-Token: <API-KEY>’ -H ‘Content-Type: application/json’ -d ‘{“type”: “reboot”}’ “https://api.packet.net/devices/<ID>/actions”

**Note**: Here I’ve specified the action type in a json body I pass to the API with the POST method, but I could have just as easily specified the action type as a query parameter, like this:

~$ curl -X POST -H ‘X-Auth-Token: <API-KEY>’ “https://api.packet.net/devices/<ID>/actions?type=reboot”

  

More information about using query parameters with the Packet API can be found [here](https://www.packet.net/developers/api/common-parameters/).

  

### **PUT and DELETE Methods**

  

So far we’ve seen only GET and POST methods. But, as was said in the beginning, DELETE and PUT are available as well. Let’s look at a few useful examples of these methods.

First up, DELETE is rather straightforward, all you need is the device ID of the device you wish to remove. For example:

~$ curl -X DELETE -H ‘X-Auth-Token: <API-KEY>’ “https://api.packet.net/device<ID>” 

The DELETE method is available for many devices and services, but the general format is the same in all cases.

PUT is generally used for updating parameters, i.e. hostnames, volume sizes, project payment methods, etc. It follows a similar structure to POST, so users will need to specify a content type and pass a json body. For example, we can update our server’s hostname and lock it with PUT like this:

~$ curl -X PUT -H ‘X-Auth-Token: <API-KEY>’ -H ‘Content-Type: application/json’ -d ‘
{
    “hostname”: “string”,
    "locked": "true"
}’ “https://api.packet.net/devices/<ID>” | jq ‘.’

  

### **Conclusion**

  

This article is meant as a brief introduction to our API, but it by no means covers the breadth and depth of control the Packet API has to offer. There is much and more that can be done via the Packet API, but most calls use more or less the same formats as covered in the examples found in this article. For more information on our RESTful API please visit our [full documentation](https://www.packet.com/developers/api/).