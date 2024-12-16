# Building Ceph

## 1. Create a Ceph Cluster
### 1.1 Create Switch Engine Machines
First we have created the four machines monitor01, osd01, osd02 and osd03 with each initial 10GB Disk Space for the OS and additional 40GB volumes that are attached to it.

### 1.2 Install docker or podman on all machines (monitor01, osd01, osd02, osd03)
Add missing statements...

### 1.3 Install Cephadm
Add missing statements...

### 1.4 Boostrap a new Cluster (monitor01)
`sudo cephadm bootstrap --mon-ip 10.0.5.77`

### 1.5 Copying SSH-Keys to osd machines
Copy the ssh-key from /root/.ssh/authorized_keys to the three osd machines (osd01, osd02, osd03) and test if you cann ssh from monitor01 to the osd machines.

### 1.6 Install LVM2 on the osd machines (osd01, osd02, osd03)
`sudo apt-get update`\
`sudo apt-get install lvm2`

### 1.7 Adding hosts to the cluster (monitor01)
`sudo cephadm shell ceph orch host add osd01 10.0.2.171`\
`sudo cephadm shell ceph orch host add osd02 10.0.3.21`\
`sudo cephadm shell ceph orch host add osd03 10.0.0.113`

### 1.8 Verifying if everything works as expected
`sudo cephadm shell ceph orch device ls`\
`sudo cephadm shell ceph osd tree`\
`sudo cephadm shell ceph status`\
`sudo cephadm shell ceph health`

## 2. Block Storage, rbd

### 2.1 First Section
Add missing statements...

## 3. File Storage, CephFS

### 3.1 First Section
Add missing statements...

## Technical Problems
On which steps above did you have technical problems? 
What was the problem? How did you solved it? 
If you proceed to Part 3, how did you implement the load balancing?
Make subparagraphs for each point. 
Make this summary together in your group.

### Can't use ceph command
* Step: Access to ceph command
* What: Encountered a "command not found" or permission error when trying to run `ceph` directly on the host.
* Solved via: Using `sudo cephadm shell` before the command to enter the Ceph container environment, ensuring that the `ceph` CLI and its dependencies are properly available.

### Can't add Hosts to Cluster
* Step: Adding new Host to Cluster
* What: Missing LVM2 Packages on osd Hosts
* Solved via: Add the missing lvm2 packages on all osd hosts via `sudo apt-get install lvm2`

## Personal reflection

Now that you build up the cluster: What did you learn personally regarding setting up a Ceph-cluster. Get as technical as possible. Make this part for yourself.

### Orkun
Networking and SSH Keys: Ensuring proper IP configuration and passwordless SSH access between nodes was crucial. Any misconfiguration here can block deployment.
OSD Management and LVM: Understanding how Ceph manages storage through LVM was important. Installing lvm2 on OSD hosts ensures smooth OSD creation and management.
Verification and Health Checks: Running status commands (ceph status, ceph health, ceph orch device ls) reinforced how critical it is to regularly verify cluster health and performance.
Access Through cephadm shell: Gaining familiarity with cephadm shell gave me a clean, consistent way to run Ceph commands without leaving the host environment. This provided quick access to health checks, device listings, and status reports, ensuring smoother troubleshooting and cluster management.

### Timon
Docker Dependency: I learned that Ceph relies on Docker - you need to install Docker first on all nodes, or nothing works.
Storage Types: Ceph can handle both block storage (RBD) and file storage (CephFS)
Automation: Writing a script and then executing this is an good approach, if i do mistake it is easier to delete everything and then simply running script again, but writing script also feels like it takes more time (instead of spamming commands into the shell until something works : D)
Pool Creation: Pool creation needs proper PG (Placement Group) settings - without enabling pg_autoscale_mode, the pool won't work efficiently
