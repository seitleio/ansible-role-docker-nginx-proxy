Ansible Role: docker-nginx-proxy
=========
[![Ansible Lint](https://github.com/seitleio/ansible-role-docker-nginx-proxy/actions/workflows/ansible-lint.yaml/badge.svg)](https://github.com/seitleio/ansible-role-docker-nginx-proxy/actions/workflows/ansible-lint.yaml)

This role creates a [nginx-proxy](https://github.com/nginx-proxy/nginx-proxy/) container that automatically attaches containers to the proxy_network based on environment variables.

Requirements
------------

As a prerequisite Python 3, Python Pip and Python docker module are required on the target host. You can install the packages manually or via ansible (see [Example Playbook](#example-playbook)).

```bash
# Manually install the packages with apt
apt install python3-full python3-pip python3-docker
```

Role Variables
--------------

| name                          | purpose | default value                       | note |
| ----------------------------- | ------- | ----------------------------------- | ---- |
| service_name                  | Used for the docker container name and the data path. | "nginx-proxy"                       |      |
| service_data_location         | All data created by this service will be stored here. | "/data/services/{{ service_name }}" |      |
| acme_admin_email              | Mail address for mail notifications from Let's Encrypt | "info@seitle.io"                    |      |
| nginx_proxy_buffer_size       |         | "128k"                              |      |
| nginx_proxy_buffers           |         | "4 256k"                            |      |
| nginx_proxy_busy_buffers_size |         | "256k"                              |      |
| nginx_client_max_body_size    |         | "100m"                              |      |

Attaching to containers
-----------------------

The role gets all containers that have the environment variable VIRTUAL_HOST defined. This containers will be connected to the proxy_network docker network. Also, when a container has a NGINX_CONFIG_* environment variable defined, this will be added to a seperate configuration file for the virtual host.

### Example
To overwrite the general client_max_body_size, we just need to set the following environment variable.

```
NGINX_CONFIG_CLIENT_MAX_BODY_SIZE: "4g"
```
When VIRTUAL_HOST has the value seitle.io, the file `data/services/nginx-proxy/vhost.d/nextcloud.seitle.io` will be created. The content of the would be the following.
```nginx
client_max_body_size 32g;
```

The file is automatically included by the nginx-proxy container. Checkout https://github.com/nginx-proxy/nginx-proxy/tree/main/docs#per-virtual_host for more information.

Dependencies
------------

* https://github.com/geerlingguy/ansible-role-docker

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
- name: Install docker on docker_hosts
  hosts: docker_hosts
  gather_facts: true
  tags:
    - setup_docker
  pre_tasks:
  - name: Install requirements for Docker Ansible module
    ansible.builtin.apt:
      pkg:
      - python3-full
      - python3-pip
      - python3-docker
    when: ansible_distribution == 'Debian' or ansible_distribution == 'Ubuntu'
  roles:
    - role: ansible-role-docker
```

License
-------
MIT

Author Information
------------------
Johannes Seitle <<johannes@seitle.io>>

<br/><br/><hr/>
<p align="center" style="font-size:24px">
<img src="https://avatars.githubusercontent.com/u/102231325?s=400&u=0c500c28b968020e0c306478e55779ed7a872a98&v=4" width="128"/><br/>
seitle.io
<p/>
