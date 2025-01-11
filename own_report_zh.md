# Building Ceph

## 1. Create a Ceph Cluster
### 1.1 Create Switch Engine Machines
First we have created the four machines monitor01, osd01, osd02 and osd03 with each initial 10GB Disk Space for the OS and additional 40GB volumes that are attached to it.

### 1.2 Install docker on all machines (monitor01, osd01, osd02, osd03)
`sudo apt-get update`\
`sudo apt-get install -y ca-certificates curl`\
`sudo install -m 0755 -d /etc/apt/keyrings`\
`sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc`\
`sudo chmod a+r /etc/apt/keyrings/docker.asc`

`echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`

`sudo apt-get update`\
`sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`


### 1.3 Install Cephadm (monitor01)``
wget -q -O- 'https://download.ceph.com/keys/release.asc' | sudo apt-key add -    
CODENAME=\$(lsb_release -sc)
echo deb https://download.ceph.com/debian-squid/ \$CODENAME main | sudo tee /etc/apt/sources.list.d/ceph.list
sudo apt-get update
sudo apt-get install -y ceph ceph-mds

### 1.4 Boostrap a new Cluster (monitor01)
`sudo cephadm bootstrap --mon-ip 10.0.8.245`

### 1.5 Copying SSH-Keys to osd machines
Copy the ssh-key from /root/.ssh/authorized_keys to the three osd machines (osd01, osd02, osd03) and test if you cann ssh from monitor01 to the osd machines.

### 1.6 Install LVM2 on the osd machines (osd01, osd02, osd03)
`sudo apt-get update`\
`sudo apt-get install lvm2`

### 1.7 Adding hosts to the cluster (monitor01)
`sudo cephadm shell ceph orch host add osd01 10.0.0.199`\
`sudo cephadm shell ceph orch host add osd02 10.0.5.147`\
`sudo cephadm shell ceph orch host add osd03 10.0.3.249`

### 1.8 Verifying if everything works as expected (monitor01)
`sudo cephadm shell ceph orch device ls`\
`sudo cephadm shell ceph osd tree`\
`sudo cephadm shell ceph status`\
`sudo cephadm shell ceph health`

## 2. Block Storage, rbd

### 2.1 Create rbd pool (monitor01)
`sudo cephadm shell -- ceph osd pool create 01-rbd-cloudfhnw`\
`sudo cephadm shell -- ceph osd pool set 01-rbd-cloudfhnw pg_autoscale_mode on`\
`sudo cephadm shell -- rbd pool init 01-rbd-cloudfhnw`

### 2.2 Create rbd client (monitor01)
`sudo cephadm shell -- ceph auth get-or-create client.01-cloudfhnw-rbd mon "profile rbd" osd "profile rbd pool=01-rbd-cloudfhnw" mgr "profile rbd pool=01-rbd-cloudfhnw"`\
`sudo cephadm shell -- ceph auth get client.01-cloudfhnw-rbd`

### 2.3 Create rbd image (monitor01)
`sudo cephadm shell -- rbd create --size 2048 01-rbd-cloudfhnw/01-cloudfhnw-cloud-image`\
`sudo cephadm shell -- rbd ls 01-rbd-cloudfhnw`

## 3. File Storage, CephFS

### 3.1 Create CephFS Filesystem (monitor01)
`sudo cephadm shell -- ceph fs volume create cephfs-cloudfhnw`\
`sudo cephadm shell -- ceph fs ls`

### 3.2 Create CephFS client (monitor01)
`sudo cephadm shell -- ceph auth del client.02-cloudfhnw-cephfs 2>/dev/null`\
`sudo cephadm shell -- ceph fs authorize cephfs-cloudfhnw client.02-cloudfhnw-cephfs / rw`\
`sudo cephadm shell -- ceph auth get client.02-cloudfhnw-cephfs`\


## Technical Problems

### Can't use ceph command
* Step: Access to ceph command
* What: Encountered a "command not found" or permission error when trying to run `ceph` directly on the host.
* Solved via: Using `cephadm add-repo --release squid` & `cephadm install ceph-common` to install the ceph cli, ensuring that the `ceph` CLI and its dependencies are properly available.

### Can't add Hosts to Cluster
* Step: Adding new Host to Cluster
* What: Missing LVM2 Packages on osd Hosts
* Solved via: Add the missing lvm2 packages on all osd hosts via `sudo apt-get install lvm2`

## Personal reflection

Now that you build up the cluster: What did you learn personally regarding setting up a Ceph-cluster. Get as technical as possible. Make this part for yourself.

### Orkun
* Networking and SSH Keys: Ensuring proper IP configuration and passwordless SSH access between nodes was crucial. Any misconfiguration here can block deployment.
* OSD Management and LVM: Understanding how Ceph manages storage through LVM was important. Installing lvm2 on OSD hosts ensures smooth OSD creation and management.
* Verification and Health Checks: Running status commands (ceph status, ceph health, ceph orch device ls) reinforced how critical it is to regularly verify cluster health and performance.
* Access Through cephadm shell: Gaining familiarity with cephadm shell gave me a clean, consistent way to run Ceph commands without leaving the host environment. This provided quick access to health checks, device listings, and status reports, ensuring smoother troubleshooting and cluster management.

### Timon
* Docker Dependency: I learned that Ceph relies on Docker - you need to install Docker first on all nodes, or nothing works.
* Storage Types: Ceph can handle both block storage (RBD) and file storage (CephFS)
* Automation: Writing a script and then executing this is an good approach, if i do mistake it is easier to delete everything and then simply running script again, but writing script also feels like it takes more time (instead of spamming commands into the shell until something works : D)
* Pool Creation: Pool creation needs proper PG (Placement Group) settings - without enabling pg_autoscale_mode, the pool won't work efficiently
