> _Use SSH to securely access your Packet servers._


SSH keys are one of the most secure ways to access a web server, since it requires authentication beyond a simple password. While each new Packet server has a root password assigned, it is removed from the customer portal after 24 hours - after which point you’ll need to leverage SSH or need to have added a new root password to the machine.
  

### **Generating SSH Keys**

  

#### The Mac/Linux Way

This is pretty simple. The first step is to create a key pair on your local machine or your work station. Open a Command line and type:

`ssh-keygen -t rsa`

Once you have entered the ssh-keygen command, you will go through with these questions.

````
Enter file in which to save the key (/home/name/.ssh/id\_rsa):

  

You can use the default name and destination if this is your first ssh key. 

Enter passphrase (empty for no passphrase):
````
  

Usually, System Administrators/DevOps do not use passphrase on their keys -- well admit it, one reason we use ssh key is to gain a secure, passwordless login to the server, right?

But if you feel the need of extra layer security, you may go ahead and key in your passphrase (make sure you safe keep your passphrase!)

Once you are done, `id_rsa.pub` is the key that you need to upload to Packet Portal. It should go without saying, but we'll say it anyway, do not share your private key _(located here /home/yourname/.ssh/id\_rsa)_ with anyone!

Your public SSH key is commonly located here _/home/yourname/.ssh/id\_rsa.pub_ and you can display your public SSH key with the following command:

`cat ~/.ssh/id\_rsa.pub`

  

### **The PuTTY Way (Windows)**

  

First, Download [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). The two binaries you will need are:

*   PuTTY (the SSH and Telnet client itself) 
*   PuTTYgen (an RSA and DSA key generation utility) 

Next, open PuTTYgen.exe which will look like this:

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/43/42170WAZCAWSKXBPXXTY0-1539834047855.png)

  

You can change the parameters for your key, even though the default ones are just fine. When you’re ready, click **Generate**.

In order to create a random key, you will be asked to move the cursor around a small empty area on the window. This randomness is called **entropy** and is used to create keys in a secure way that cannot be reproduced by others.

After a few seconds, once the keys are ready, you will be presented with this view:

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/43/42172NQZNRCSYBTBQWZW0-1539834048895.png)

  

Click the **Save private key** button, name it whatever you like and choose a secure location to save the key with the extension ".ppk".

Repeat the same thing after clicking on **Save public key**. This time, make sure to give it an extension like ".txt", so you can open it later in a regular text editor.

**Note!** _If you open the public key text file you just saved, you will probably see that it contains something that looks like the following:_

  

````
--- BEGIN SSH2 PUBLIC KEY ----_

_Comment: "rsa-key-xxxxxx"_

_...._

_\---- END SSH2 PUBLIC KEY ----_
````
  

_Why Putty saves it in this format is unclear, but it is not an accepted form of adding the public key._

_So, for that reason, you might want to copy whatever the Putty Generator shows on the Public key window and paste it on the document, after deleting everything that was there before._

It should look like: 
````
ssh-rsa AAAA............== rsa-key-xxxxxxx
````


### **Adding Your Key to the Packet Portal**


Once you have generated an SSH key pair you can upload the public key to your account. Via the packet portal, go to "SSH Keys" on the left-hand side and click "Add SSH Key" In the lower right-hand corner.

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/43/42174RZAABNJYGMYSAAM0-1539834049561.png)

  


#### Understanding SSH at Packet -- Personal Keys vs Project Keys


When adding a key in the Packet Portal, you can choose to add either Project Key or a Personal Key.

  
![](https://deskpro-cloud.s3.amazonaws.com/files/26944/43/42176CTRDZTBRTMWKYWC0-1539834050037.png)

  

As you can see, a Personal Key will be included on all new machines in the projects that you own, or of which you are a collaborator.

You can also choose to create and manage a key that is specific to a single project (which will be included by default on servers deployed into a particular project).  This 2nd option is useful if you don’t want to use a personal key that you leverage in lots of places on a shared box.


#### **Getting Your Key(s) on Your Server(s)**

We use our cloud-init service to add all the selected keys (Personal + Project specific + Collaborator) onto each machine at provision time. So as soon as your box is deployed, you can access it via SSH. Nice!

This also means that any keys you (or your collaborators) add via the Packet portal after a server is provisioned won’t be available on the machine automatically.  You’ll have to add new keys. Here are a couple of different situations:



#### **Adding New SSH Keys to An Existing Server**

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/43/42175ZYKWGTJMQNMRKPG0-1539834049300.png)

  

Add Your New SSH Key to the Portal:

1.  Navigate to the SSH Keys section of the Packet portal.
2.  Add your public SSH key. Note that below the key value area, you will see an option to associate this new key with a specific server. You may also select “all of them” if you have many existing servers.  Be sure to select the servers that need this new SSH key!

Now that you that the key is added in portal, you need to force add it to your server(s). 

Use our SOS service to login with (root + pw) and manually add the new key on the authorized\_keys file.


#### **Logging In Via SSH**

  

**Mac/Linux  
**SSH access on Mac and Linux is straightforward. Simply run the command:

ssh root@<your\_Public IPv4>

  

#### Windows
Run the PuTTY.exe binary downloaded earlier go to "Data" under "Connection**", and add **root** in the field of the username.

  

Go to Authentication, under SSH, and click the Browse button, to add the private SSH key created earlier.

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/43/42171KGCJCXSTSWXMCNB0-1539834048705.png)

  

Now go to **Session**, enter the public IP address of your server, give a name to the session, and click **Open**.

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/43/42173JCYNMRNWMWZAYMM0-1539834049062.png)

Et voila, you now have SSH access to your server and can continue on with your day.