# vagrant-rancher
Create a rancher environment using vagrant, based on Chris unwin's rancher rodeo demo example (https://github.com/chrisurwin/rancher-rodeo).

## Usage

`vagrant up`

Creates a rancher master on docker 1.12, latest version of rancher. Joins n (2 by default) nodes which are then joined to the rancher master. Once stood up, view your shiney new rancher cluster at: http://172.22.101.100:8080

`vagrant destroy`

Banish your rancher cluster

## Configuration

Modify your Vagrantfile to change the node memory allocation, as well as number of nodes. 

Default memory allocation is:

Master: 1042 Mb
Node: 2048 Mb
