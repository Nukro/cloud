# Building Ceph

## Create a Ceph Cluster
* Create Switch Engine Machines
First we have created the four machines monitor01, osd01, osd02 and osd03 with each initial 10GB Disk Space for the OS and additional 40GB volumes that are attached to it

## Building up the platform together

Describe all steps building the cluster with commands.


On which steps above did you have technical problems? What was the problem? How did you solved it? If you proceed to Part 3, how did you implement the load balancing?
Make subparagraphs for each point. 
Make this summary together in your group.

### [Example] Lorem Ipsum....
* Step: [Example] Initializing wrong parameter
* What: [Example] Container Runtime was not working
* Solved via: [Example] adaption of config

## Personal reflection

Now that you build up the cluster: What did you learn personally regarding setting up a Ceph-cluster. Get as technical as possible. Make this part for yourself.

### [Example] Sebastian

* [Example] Workload-1: Generating a load balancer with quite hard....

### Timon
Docker Dependency: I learned that Ceph relies on Docker - you need to install Docker first on all nodes, or nothing works.
Storage Types: Ceph can handle both block storage (RBD) and file storage (CephFS)
Automation: Writing a script and then executing this is an good approach, if i do mistake it is easier to delete everything and then simply running script again, but writing script also feels like it takes more time (instead of spamming commands into the shell until something works : D)
Pool Creation: Pool creation needs proper PG (Placement Group) settings - without enabling pg_autoscale_mode, the pool won't work efficiently
