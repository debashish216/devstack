## Cloud Platform Deployment using devstack (Openstack Cloud Platform Service)

In this example, a multi node openstack environment is demonstrated. The setup is a minimal realization having one controller node and one compute node. Both the nodes are created on top of two x86 machines with ubuntu 16.04 distribution.   

### Specifications
Software = devstack
Operating System = Ubuntu 16.04 
Other Ubuntu distributions also work fine.

## Step-by-step Installtion Procedure
### Controller Node

#### System Requirement
4+ core CPU, 100+ GB Storage, 8+ GB RAM

#### Getting the software

```
git clone https://git.openstack.org/openstack-dev/devstack -b stable/newton
```

#### Environment Setup
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

* Edit the local.conf file as below in the controller node.

```
[[local|localrc]]
HOST_IP=<host IP>
SERVICE_HOST=<host IP>
MYSQL_HOST=<host IP>
RABBIT_HOST=<host IP>
GLANCE_HOSTPORT=<host IP>:9292
ADMIN_PASSWORD=<devstack admin password>
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD

## Neutron options
Q_USE_SECGROUP=True
FLOATING_RANGE="192.168.130.0/24"
IPV4_ADDRS_SAFE_TO_USE="10.0.0.0/22"
Q_FLOATING_ALLOCATION_POOL=start=192.168.130.235,end=192.168.130.254
PUBLIC_NETWORK_GATEWAY="192.168.130.1"
PUBLIC_INTERFACE=<Your network interface such as eth0>

# Open vSwitch provider networking configuration
Q_USE_PROVIDERNET_FOR_PUBLIC=True
OVS_PHYSICAL_BRIDGE=br-ex
PUBLIC_BRIDGE=br-ex
OVS_BRIDGE_MAPPINGS=public:br-ex
```

#### Run Instructions

```
cd devsrack
./stack.sh
```

This installation procedure normally takes 30-45 minutes depending upon the Internet speed. In case of any errors,
unstack it and rerun after fixing the issue.

```
./unstack.sh
./stack.sh
```

* If you receive a message like below at the end of command execution, then the installtion is fine and successful.

```
This is your host IP address: 192.168.130.197
This is your host IPv6 address: fe80::8a28:e90a:e3a8:ec90
Horizon is now available at http://192.168.130.197/dashboard
Keystone is serving at http://192.168.130.197/identity/
The default users are: admin and demo
The password: mypassword123
```

* Login to openstack dashboard "Horizon" by using obtaned user credentials. In the above case, open a browser window and paste host IP address (192.168.130.197). Login by username "admin" and password as "mypassword123" as set by you in the local.conf file.

* Congratulations! You have successfully setup the controller node on your first ubuntu machine using devstack.
