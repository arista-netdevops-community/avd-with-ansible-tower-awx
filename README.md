![](https://img.shields.io/badge/Arista-CVP%20Automation-blue) ![](https://img.shields.io/badge/Arista-EOS%20Automation-blue) ![Ansible Code Execution](https://github.com/arista-netdevops-community/ansible-avd-cloudvision-demo/workflows/Ansible%20Code%20Execution/badge.svg) ![GitHub](https://img.shields.io/github/license/arista-netdevops-community/ansible-avd-cloudvision-demo)
# Arista Validated Design with CloudVision deployment

## About

This example shows how to deploy basic __EVPN/VXLAN Fabric__ based on __[Arista Validated Design roles](https://github.com/aristanetworks/ansible-avd)__ using Ansible Tower/AWX. This repository will be used as project on AWX and we will describe how to configure Tower for the following topics:

- Create a project
- Create inventory
- Install collections
- Install python requirements

## Requirements

To play with this repsoitory, you need:

- An AWX setup running on either Docker Compose or Kubernetes. All the commands for Python configuration will be done on docker-compose, but you can adapt for kubernetes.
- Understanding of how to configure AVD in a pure Ansible CLI way.

## Install Python requirements

Ansible CVP collection comes with a needs of [additional libraries](requirements.txt) not part of a standard Python setup:

```shell
ansible==2.9.6
netaddr==0.7.19
Jinja2==2.10.3
requests==2.22.0
treelib==1.5.5
cvprac==1.0.4
paramiko==2.7.1
jsonschema==3.2.0
```

### Create virtual-environment

It is required to create virtual-env to not impact other workflow already deployed on your Tower setup.

```shell
# Docker status
tom@kube-tool:~$ docker ps
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                  NAMES
4a4627b21f93        ansible/awx:15.0.0   "/usr/bin/tini -- /u…"   8 days ago          Up 8 days           8052/tcp               awx_task
6ef41f162226        ansible/awx:15.0.0   "/usr/bin/tini -- /b…"   8 days ago          Up 8 days           0.0.0.0:80->8052/tcp   awx_web
a2fd85d0cc86        postgres:10          "docker-entrypoint.s…"   8 days ago          Up 8 days           5432/tcp               awx_postgres
573d03e33c44        redis                "docker-entrypoint.s…"   8 days ago          Up 8 days           6379/tcp               awx_redis

# Run shell in docker
tom@kube-tool:~$ docker exec -it awx_task bash

$ sudo pip3 install virtualenv
WARNING: Running pip install with root privileges is generally not a good idea. Try `pip3 install --user` instead.
Requirement already satisfied: virtualenv in /usr/local/lib/python3.6/site-packages

$ mkdir /opt/my-envs

$ chmod 0755 /opt/my-envs

$ cd /opt/my-envs/

$ python3 -m venv avd-venv
```

> This configuration __MUST__ be replicated on both container `awx_task` and `awx_web`

Instruct AWX to register our new Virtual Environment folder:

```shell
$ curl -X PATCH 'http://admin:password@<IP-of-AWX-INSTANCE>/api/v2/settings/system/' -d '{"CUSTOM_VENV_PATHS": ["/opt/my-envs/"]}' -H 'Content-Type:application/json'

{"ACTIVITY_STREAM_ENABLED":true,"ACTIVITY_STREAM_ENABLED_FOR_INVENTORY_SYNC":false,"ORG_ADMINS_CAN_SEE_ALL_USERS":true,"MANAGE_ORGANIZATION_AUTH":true,"TOWER_URL_BASE":"http://10.83.28.163","REMOTE_HOST_HEADERS":["REMOTE_ADDR","REMOTE_HOST"],"PROXY_IP_ALLOWED_LIST":[],"LICENSE":{},"REDHAT_USERNAME":"","REDHAT_PASSWORD":"","AUTOMATION_ANALYTICS_URL":"https://example.com","INSTALL_UUID":"f8a54d56-b1f3-4fdf-aa5b-9d6977d00eaa","CUSTOM_VENV_PATHS":["/opt/my-envs"],"INSIGHTS_TRACKING_STATE":false,"AUTOMATION_ANALYTICS_LAST_GATHER":null,"AUTOMATION_ANALYTICS_GATHER_INTERVAL":14400}
```

### Provision virtual-environment

Before running playbook in a virtual-env, we have to install required libraries:

```shell
tom@kube-tool:~$ docker exec -it awx_task bash

$ cd /opt/my-envs/avd-venv

$ vi requirements.txt

$ source bin/activate

$ pip3 install psutil

$ pip3 install -r requirements.txt
```

## Create a new project

### Create Project

### Create Inventory

### Create Playbook


## Resources

- Ansible [Arista Validated Design](https://github.com/aristanetworks/ansible-avd) repository.
- [Ansible CloudVision Collection](https://github.com/aristanetworks/ansible-cvp) repository.
- [How to install](INSTALLATION.md) demo environment.
- [Detailed demo script](DEMO.md).

## License

Project is published under [Apache License](LICENSE).

## Problem & Links

- Virtual Env:
  - https://medium.com/@mehdirashidi/setting-up-venv-in-awx-31c4f9952a46
  - https://github.com/ansible/awx/issues/4140
- Inventory:
  - https://github.com/ansible/awx/issues/5926