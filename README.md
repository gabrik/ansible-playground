# Ansible playgroud

This repo contains some playbooks and roles that I'm using to learn and experiment
with Ansible.


## Prerequisites

```
pip3 install libvirt-python lxml
ansible-galaxy collection install community.libvirt
ansible-galaxy collection install community.general
ansible-galaxy install nvidia.nvidia_driver
```

## Roles

- kvm_provison: this roles allows to spawn (and destroy) VMs and virtual networks on libvirt
- lxd_provison: this roles allows to spawn (and destroy) containers with LXD
- net_provison: this roles allows to create and destroy virtual networks and interfaces (WIP)

## Playbooks

### manage-vm-net.yml

This playbook allows to create (and delete) a virtual network on libvirt

Creating a network example:
```
ansible-playbook -i inventory manage-vm-net.yml --tags net_create -e name=example-net -e gw_addr=192.168.33.1 dhcp_start=192.168.33.10 dhcp_end=192.168.33.20
```


Deleting a network example:
```
ansible-playbook -i inventory manage-vm-net.yml --tags net_delete -e name=example-net
```

### manage-vm.yml

This playbook allows to create (and delete) a VM on libvirt

Creating a VM example:
```
ansible-playbook -i inventory manage-vm.yml -e disk_gb=10 -e vcpus=4 -e ram_mb=8192 -e vm=myvm  --tags create
```

Creating a vm attached to multiple networks
```
ansible-playbook -i inventory manage-vm.yml -e disk_gb=10 -e vcpus=4 -e ram_mb=8192 -e vm=myvm  --tags create --extra-vars '{"net":["default","example-net"]}'
```

Deleting a VM:
```
ansible-playbook -i inventory manage-vm.yml vm=myvm  --tags delete
```

### manage-container.yml

This playbook allows to create (and delete) a VM on LXD

Creating a Container example:
```
ansible-playbook -e host=localhost  manage-container.yml -e disk_gb=5 -e vcpus=16 -e ram_mb=8192 -e name=test --tags create
```

Creating a Container attached to multiple networks:
```
ansible-playbook -e host=localhost  manage-container.yml -e disk_gb=5 -e vcpus=16 -e ram_mb=8192 -e name=test --extra-vars '{"nets":["lxdbr0","virbr0"]}' --tags create
```

Deleting a Container:
```
ansible-playbook -e host=localhost  manage-container.yml -e name=test --tags delete
```


### playbook-zenoh.yml

This playbook allow to build and install or test zenoh on an debian-based machine.
Without the `host` parameter will run on `localhost`

Example build and test:
```
ansible-playbook -i vm-inventory.yml -e host=zenoh -e user=ato playbook-zenoh.yml -t test
```

Example build and install:
```
ansible-playbook -i vm-inventory.yml -e host=zenoh -e user=ato playbook-zenoh.yml -t install
```


### playbook-zenoh-flow.yml

This playbook allow to build and install or test zenoh-flow on an debian-based machine.
Without the `host` parameter will run on `localhost`

Example build and test:
```
ansible-playbook -i vm-inventory.yml -e host=zf -e user=ato playbook-zenoh-flow.yml -t test
```

Example build and install:
```
ansible-playbook -i vm-inventory.yml -e host=zf -e user=ato playbook-zenoh-flow.yml -t install
```


### nvidia-driver.yml

This playbook allow to install the NVIDIA drivers.
Without the `host` parameter will run on `localhost`

Example:
```
ansible-playbook -i vm-inventory.yml -e host=gpuvm nvidia-driver.yml
```
