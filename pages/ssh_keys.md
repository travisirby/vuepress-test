If you haven’t already registered for an account, you should definitely start there - [register for an account](https://app.packet.net/#/registration/). You won’t be charged anything but you will need a valid credit card or a PayPal account associated with a valid credit card to finalize your registration.  
  

_⚠️**Heads up**: we are serious about keeping our user base free from abuse and fraud, since one bad apple can cause a fair amount of problems. Don’t be surprised if your account is flagged for a quick manual review, which may involve validating your identity. If you’re looking to avoid it, please use a corporate email address if available, and don’t sign up while logged in to a VPN, etc._

  

### **Create a Project**

  

Before you can provision a server you’ll need to create a project. A project allows you to organize groups of servers and collaborators. To create a project open the drop-down in the upper right and choose "Settings -> Projects".  The [portal should guide](https://support.packet.com/kb/articles/portal) you from there.

  

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/426/425592BJTTSXQQBBRRPQK0-Screen-Shot-2018-12-18-at-2.44.51-PM.png)

  

---

### Upload an SSH Key to Project Settings

  

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/426/425655YDGDGPRHCBNTXBN0-Screen-Shot-2018-12-18-at-2.52.17-PM.png)

  

Generating an SSH key is required, as root password access is disabled. For more help with generating SSH keys, check out [this guide](https://support.packet.com/kb/articles/generate-ssh-keys).

  

---

### **Deploy a Server**

  

Navigate to your project's homepage (upper left) to deploy your first bare metal server. The portal will guide you but you should probably have a look at the [pricing structure](https://www.packet.net/bare-metal/) if haven’t already.

  

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/426/425678PZQPMTRXDJYZBDX0-Screen-Shot-2018-12-18-at-2.53.45-PM.png)

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/275/274335GHKNJQAAPKMDSRT0-Screen-Shot-2018-11-28-at-11.39.39-AM.png)

  

On-demand is in the most common deployment choice. On the next screen, you can name your device, choose a location, OS and any user data/keys you need to add. 

  

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/275/274364XPMDXCXHSZPYAYZ0-Screen-Shot-2018-11-28-at-11.41.17-AM.png)

  

### **Connect To Your Server**

  

Connecting to your server requires an SSH client; generally, that means using a command line like Terminal for macOS but you can also download an SSH tool of your choice, i.e. PuTTY for Windows. First, grab your server’s public IPv4 address from the server’s detail page in the portal.

  

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/34/33910MKCRYNSMAJHDZSS0-1539746792966.png)

  

If you’re using a command line, do this from your ssh directory ( in our case that’s _~/.ssh/_):

  

**ssh -i id\_rsa root@\[ you server’s public IPv4\]**

  

![](https://deskpro-cloud.s3.amazonaws.com/files/26944/34/33957ZHHHYTCGYCMXYDR0-1539747026700.png)
