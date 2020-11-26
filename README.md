# ansible-magicmirror
A MagicMirror deployment tool using Ansible and Docker

NOTE: only supported to deploy on Raspberry Pi OS

# Overview

Deployment is mainly executed with the following two steps.

1. Install docker on Raspberry Pi
2. Setup MagicMirror server as a docker container

After the deployment is completed successfully, you can find the docker container running on Raspberry Pi.
```
pi@raspberrypi:~ $ docker ps
CONTAINER ID        IMAGE                                     COMMAND                  CREATED             STATUS              PORTS               NAMES
89e1600d8faf        bastilimbach/docker-magicmirror:v2.12.0   "./docker-entrypoint…"   2 hours ago         Up 29 minutes                           magicmirror
```

To access the MagicMirror, you can use built-in browser in Raspberry Pi with full-screen mode.


# How to use

## Preparation

`ansible` must be installed on your local environment to execute playbook.

```
$ pip install ansible
```
or you can use `poetry` to install all dependencies.
```
$ poetry install
```

## Configuration

First, you must prepare inventory file. A sample inventory file is placed as `pi_hosts.yml`.
Please edit the host list and their variables.
```
# pi_hosts.yml

magicmirror:
  hosts:
    pi-server:
  vars:
    ansible_ssh_user: pi
    ansible_python_interpreter: /usr/bin/python
    proxy_env: {}
    magicmirror_version: v2.12.0
```

`magicmirror_version` must be one of available docker image tags described in https://hub.docker.com/r/bastilimbach/docker-magicmirror/tags.

There are several configuration option for MagicMirror server. You can override the default values defined in `roles/magicmirror/defaults/main.yml` by Ansible host vars or extra vars.

If you are behind the proxy and you have to set http proxy to access Internet, you can set `proxy_env` as:
```
proxy_env:
  http_proxy: xxxx
  https_proxy: xxxx
```

## Using custom modules

You can import your custom modules from Github repository by setting `magicmirror_custom_modules`.

For example,
```
magicmirror_custom_modules:
  - name: MMM-XXXX
    repo: https://github.com/mtatsuma/MMM-XXXX.git
    version: master
```

Configulation for the custom module is also set in `magicmirror_module_config`.
```
magicmirror_module_config:
  - module: MMM-XXXX
    position: top_right
    config:
      param1: value1
      param2: value2
```

## Using custom CSS

You can add custom CSS by setting `magicmirror_custom_css` variable.

For example,
```
magicmirror_custom_css: >-
  .module-content {
    border: 1px solid;
  }
```

## Deploy MagicMirror on Raspberry Pi

You can run playbook `site.yml` to deploy MagicMirror.

```
$ ansible-playbook -i pi_hosts.yml site.yml
```
