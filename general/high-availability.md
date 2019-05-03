# High Availability

## **Is Packet bare metal designed for high availability?**

Yes! We deploy our Portals, website, API and associated micro-services in an HA environment, so we have thought a lot about how to make Packet a great platform for highly available application deployments.

All our servers have dual, bonded network interfaces, which are cabled to different top of rack switches, which in turn connect to redundant core routers. Basically, 2N redundant at the server, rack, and facility level.

Another thing that sets us apart from other colocation or dedicated service providers is our metadata service. It allows you to define User Data for Cloud-init, which paves the way for service discovery, application installation, or whatever else you need for bootstrapping a node into a cluster.  


