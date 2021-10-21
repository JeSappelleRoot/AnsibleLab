- [AnsibleLAB](#ansiblelab)
	- [Quick demo](#quick-demo)
- [Ansible inventory](#ansible-inventory)
	- [Groups](#groups)
- [Ansible container](#ansible-container)
	- [SSH private and public keys handling](#ssh-private-and-public-keys-handling)
	- [SSH key sharing between containers](#ssh-key-sharing-between-containers)
	- [Ignore key checking](#ignore-key-checking)
	- [Playbook handling](#playbook-handling)
	- [Role handling](#role-handling)

# AnsibleLAB

This Docker Compose environment provide a quick and temporary environment to test Ansible playbooks on several Unix system : 
- CentOS 7  
- Debian 9  
- Ubuntu 20.04  
- Fedora 33 
- Alma Linux 8.4  

**All these versions are defined in `.env` file to easily change versions if needed, feel free to adjust this environment file to match your requirements**
> Keep in mind that changing base image version could break the nodes docker builds

The Docker Compose file will generate 3 hosts for each OS family, then you will have 12 hosts and the Ansible instance. 
Ansible will handle the connection with a custom SSH private key, the public key will be shared between containers.  

## Quick demo

You can clone this repository and launch the AnsibleLab with `docker-compose up`  or `docker-compose up -d` if you prefer background execution.  
Then, you can use the ansible `ping` module to check the connectivity via docker-compose exec or directly from a /bin/bash on ansible container  : 

```
# ~ docker-compose exec ansible ansible -m ping all

centos1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
centos2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
debian1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
debian2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
debian3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
centos3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ubuntu1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
fedora1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
fedora3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
fedora2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ubuntu2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ubuntu3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
almalinux3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
almalinux2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
almalinux1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

# Ansible inventory

The inventory is handled by the file named `ansible_inventory` and will be mounted on Ansible container to under /etc/ansible/hosts with Read-Only permissions.  
Groups are defined in this file and can be easily edited

## Groups

You have different groups predefined in the inventory : 
- `centos`
- `ubuntu`
- `debian`
- `fedora`
- `almalinux`

Feel free to modify these groups to match your requirements

# Ansible container

Default root password is `root` and can be used to perform an SSH connection to the Ansible container.


## SSH private and public keys handling

In `./ansible/Dockerfile`, you can find the Ansible container build. You can notice that there is a multi-stage build :  
- the 1st build will handle the SSH keys creation, ed25519 key type, without password
- the 2nd build will hande the Ansible installation and Ansible quick configuration.

SSH keys are copied to `/etc/ansible/SSH` folder at the end of the Ansible container build.

## SSH key sharing between containers

To share the public key of the Ansible instance between containers, there is a named volume `ssh_sharing`.  
This volume will be mounted on Ansible container under `/etc/ansible/SSH` (which contains the keys of the Ansible container multi-stage).  
Then, the same volume will be mounted under `/root/.ssh` for each containers. 

> This solution is not perfect, because the private key is also shared to each containers !  

## Ignore key checking

To avoid fingerprint checking by Ansible, we are adding the environment variable `ANSIBLE_HOST_KEY_CHECKING` set to `False`.  

## Playbook handling

Playbooks will be handled by the folder `./playbooks`, this local folder will be mounted in Ansible container under `/etc/ansible/playbooks`.  
Then, you will be able to work in your playbooks locally and check the execution with the Ansible container.  

## Role handling

Roles will be handled by the folder `./roles`, this local folder will be mounted in Ansible container under `/etc/ansible/roles`.  
This will provide the same way as playbook folder


