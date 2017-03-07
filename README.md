#### Openstack Cloud Deployment using devstack

In this example, a multi node openstack environment is demonstrated. The setup is a minimal realization having one controller node and one compute node. Both the nodes are created on top of two x86 machines with ubuntu 16.04 distribution.   

##### Specifications

```
Software = devstack
Operating System = Ubuntu 16.04 
Other Ubuntu distributions also work fine.
```

##### Step-by-step Installation Procedure on Controller Node
###### System Requirement

```
4+ core CPU
100+ GB Storage
8+ GB RAM
```

###### Update and Upgrade ubuntu OS

```
sudo apt-get update
sudo apt-get dist-upgrade
sudo reboot
```

###### User Account Creation
* Create a user with sudo priviledges.

```
sudo adduser stack
```

* Give "sudo" permission to the new user typing command ```sudo visudo```. Add below line.

```
stack    ALL=(ALL:ALL) ALL
```

* Navigate to the new user.

```
sudo su - stack
```

###### Get the software
* Install "git" and clone the required software.

```
sudo apt-get update
sudo apt-get install git
git clone https://git.openstack.org/openstack-dev/devstack -b stable/newton
```

###### Environment Setup
* Navigate to devstack/samples and copy the local.conf file to devstack folder.

```
cd devstack/samples/
ls -li
cp local.conf ../
cd ..
ls -li
sudo apt-get install vim
vim local.conf 
```

* Edit the local.conf file as below in the controller node. My host IP is "192.168.130.207" and I wish to install everything with password "mypassword123". You may choose a different password for your setup.

```

[[local|localrc]]
MY_IP=192.168.130.102
HOST_IP=$MY_IP
SERVICE_HOST=$MY_IP
MYSQL_HOST=$MY_IP
RABBIT_HOST=$MY_IP
GLANCE_HOSTPORT=$MY_IP:9292
ADMIN_PASSWORD=iith
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD

## Neutron options
Q_USE_SECGROUP=True
FLOATING_RANGE="192.168.130.0/24"
IPV4_ADDRS_SAFE_TO_USE="10.0.0.0/22"
Q_FLOATING_ALLOCATION_POOL=start=192.168.130.235,end=192.168.130.254
PUBLIC_NETWORK_GATEWAY="192.168.130.1"
PUBLIC_INTERFACE=enp4s0

# Open vSwitch provider networking configuration
Q_USE_PROVIDERNET_FOR_PUBLIC=True
OVS_PHYSICAL_BRIDGE=br-ex
PUBLIC_BRIDGE=br-ex
OVS_BRIDGE_MAPPINGS=public:br-ex

VNCSERVER_LISTEN=0.0.0.0

#Enable heat plugin
enable_plugin heat https://git.openstack.org/openstack/heat stable/newton
```

###### Run Instructions

```
sudo mkdir /opt/stack
sudo chown -R stack:stack devstack
sudo chown -R stack:stack /opt/stack/
cd devstack
./stack.sh
```

This installation procedure normally takes 30-45 minutes depending upon the Internet speed. In case of any errors,
unstack it and rerun after fixing the issue.

```
./unstack.sh
./stack.sh
```

* If you receive a message like below at the end of command execution, then the installation is fine and successful.

```
=========================
DevStack Component Timing
=========================
Total runtime         3040

run_process            76
test_with_retry         9
apt-get-update         15
pip_install           157
restart_apache_server  31
wait_for_service       50
apt-get                 6
=========================



This is your host IP address: 192.168.130.102
This is your host IPv6 address: fe80::d69b:58a8:90d5:1d96
Horizon is now available at http://192.168.130.102/dashboard
Keystone is serving at http://192.168.130.102/identity/
The default users are: admin and demo
The password: mypassword123
```

* Login to openstack dashboard "Horizon" by using obtaned user credentials. In the above case, open a browser window and paste host IP address (192.168.130.197). Login by username "admin" and password as "mypassword123" as set by you in the local.conf file.

* Congratulations! You have successfully setup the controller node on your first ubuntu machine using devstack.


##### Step-by-step Installation Procedure on Compute Node
###### System Requirement

```
8+ core CPU
500+ GB Storage
8+ GB RAM
```
###### Update and Upgrade ubuntu OS

```
sudo apt-get update
sudo apt-get dist-upgrade
sudo reboot
```

###### User Account Creation
* Create a user with sudo priviledges.

```
sudo adduser stack
```

* Give "sudo" permission to the new user typing command ```sudo visudo```. Add below line.

```
stack    ALL=(ALL:ALL) ALL
```

* Navigate to the new user.

```
sudo su - stack
```

###### Get the software
* Install "git" and clone the required software.

```
sudo apt-get update
sudo apt-get install git
git clone https://git.openstack.org/openstack-dev/devstack -b stable/newton
```

###### Environment Setup
* Navigate to devstack/samples and copy the local.conf file to devstack folder.

```
cd devstack/samples/
ls -li
cp local.conf ../
cd ..
ls -li
sudo apt-get install vim
vim local.conf 
```

* Edit the local.conf file as below in the compute node.

```
[[local|localrc]]
<edit here>
```

###### Run Instructions

```
cd devstack
./stack.sh
```

This installation procedure normally takes 5-10 minutes depending upon the Internet speed. In case of any errors,
unstack it and rerun after fixing the issue.

```
./unstack.sh
./stack.sh
```

* If you receive a message like below at the end of command execution, then the installation is fine and successful.

```
=========================
DevStack Component Timing
=========================
Total runtime   866

run_process       9
apt-get-update   11
pip_install     204
git_timed       386
apt-get         224
=========================

This is your host IP address: 192.168.130.207
This is your host IPv6 address: fe80::9b06:9601:1b07:93eb

```

* Congratulations! You have successfully setup a compute node on your second ubuntu machine using devstack. You can repeat the same procedure to add multiple compute nodes to your devstack environement.
* In order to verify the availability of this newly created compute node at controller node, log in to the openstack dashboard using admin credentials, then navigate to the admin->hypervisors. You will be able to see a new node popping up on the dashboard screen.



##### Known Issues 
1. While running stack.sh, if you are getting below error, the update permission to some folders.

```
“”OSError: [Errno 13] Permission denied:
```

```
sudo chown -R stack:stack devstack
sudo chown stack: /home/stack/.cache
sudo chown -R stack:stack /opt/stack/
```

* If you are facing issue for correct path set up, export below line to the environemnt. Alternatively, you could add this line to your environment path which persists even after reboot of the machines.

```
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/sbin
```
* If any error like paste name deploy python error. then solution is as below : 

```
sudo apt-get install --reinstall  python-pastedeploy
sudo apt-get install --reinstall python-paste
```
* If any error like "AssertionError: Egg-link  does not match installed location of tempest", perform below step.

```
sudo pip install . 
sudo pip install -e .
```

#### Thank You !
