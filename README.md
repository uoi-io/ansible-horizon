Ansible OpenStack Horizon (OpenStack ready)
=========

[![Build Status](https://travis-ci.org/uoi-io/ansible-horizon.svg?branch=master)](https://travis-ci.org/uoi-io/ansible-horizon) [![Ansible Galaxy](https://img.shields.io/badge/galaxy-uoi.horizon-green.svg?style=flat)](https://galaxy.ansible.com/uoi-io/horizon/)

This role allows you to deploy an OpenStack Horizon dashboard.

This OpenStack component will be deploy in a Python ``virtualenv`` by using ``pip``. To serve the pages, Nginx and uWSGI will be our superheros !

By using ``horizon_version`` variable, we will be able to choose the OpenStack version that we want to deploy. The current versions are supported:
  - Liberty | 2015-10-15
  - Mitaka | 2016-04-07
  - Newton | 2016-10-06

Supported distributions:
  - CentOS 7.x
  - RedHat EL 7.x
  - Debian 7.x / 8.x / 9.x
  - Ubuntu 15.x / 16.x

Supported functionalities:
  - Firewalld (``iptables`` and ``firewalld`` packages are needed on the server)
  - SELinux

Requirements
------------

This role needs at least Ansible 2.x.

Role Variables
--------------

```
### HORIZON
# file: roles/horizon/defaults/main.yml
horizon_firewalld: true
horizon_selinux: true
horizon_uoi_download: false
horizon_cleaning: false

horizon_user: horizon
horizon_group: horizon
horizon_uid: 2003
horizon_gid: 2003

horizon_git_url: https://github.com/openstack/horizon.git
horizon_version: stable/newton

horizon_uwsgi_processes: 5
horizon_uwsgi_threads: 2
horizon_uwsgi_harakiri: 20
horizon_uwsgi_max_requests: 10000

horizon_nginx_bind: 0.0.0.0
horizon_internal_port: 8080
horizon_public_port: 80

# Config for /etc/openstack-dashboard/local_settings.py
horizon_config_debug: false
horizon_config_allow_hosts:
  - 10.0.0.100
horizon_config_keystone_host: 10.0.0.100
horizon_config_keystone_version: v3
horizon_config_default_role: user
horizon_config_enable_backup: true
horizon_config_enable_ha_router: true
horizon_config_multidomain_support: true
horizon_config_caching: true
horizon_config_caching_servers:
  - 172.0.0.6:11211
  - 172.0.0.7:11211
  - 172.0.0.8:11211
horizon_config_themes:
  - default
horizon_config_secret_key: "{{ lookup('password', 'chars=ascii_letters,digits length=64') }}"

# Add the possiblity to add option not in the template
horizon_config_extras: []

# Neutron LBAASv2 Horizon module
horizon_module_lbaasv2: true
horizon_module_lbaasv2_git_url: https://github.com/openstack/neutron-lbaas-dashboard.git
horizon_module_lbaasv2_version: "{{ horizon_version }}"
```

Because the role supports RedHat and Debian distributions like, we have to define some values depending of the OS family.

```
---
### DEBIAN
# file: roles/horizon/vars/Debian.yml
horizon_packages:
  - gcc
  - git
  - libffi-dev
  - python-dev
  - python-pip
  - python-setuptools
  - python-virtualenv
  - libssl-dev
  - libmariadbclient-dev
  - postgresql-server-dev-all
  - mime-support
horizon_systemd_path: /lib/systemd
horizon_uwsgi_path: /usr/local/bin/uwsgi
horizon_nginx_user: www-data
horizon_nginx_configdir: /etc/nginx/sites-enabled
```
```
---
### MAIN
# file: roles/horizon/vars/main.yml
horizon_dir: "{{ working_dir }}/virtualenvs/horizon"
working_dir: /srv/data
```
```
---
### REDHAT
# file: roles/horizon/vars/RedHat.yml
horizon_packages:
  - gcc
  - git
  - libffi-devel
  - libxslt-devel
  - python-devel
  - python-greenlet-devel
  - python-ldap
  - python-lxml
  - python-pip
  - python-setuptools
  - python-virtualenv
  - openldap-devel
  - openssl-devel
  - sqlite-devel
  - mailcap
horizon_systemd_path: /usr/lib/systemd
horizon_uwsgi_path: /usr/bin/uwsgi
horizon_nginx_user: nginx
horizon_nginx_configdir: /etc/nginx/conf.d
```

Dependencies
------------
None

Example Playbook
----------------
```
---
horizon_nginx_bind: "{{ hostvars[inventory_hostname]['ansible_bond0.10']['ipv4']['address'] }}"
horizon_uwsgi_processes: 10
horizon_firewalld: false
horizon_selinux: false
horizon_config_debug: false
horizon_config_allow_hosts:
  - 10.0.0.100
  - 172.0.0
  - localhost
  - dashboard.uoi.io
horizon_config_keystone_host: 10.0.0.100
horizon_config_keystone_version: v3
horizon_config_default_role: user
horizon_config_enable_backup: true
horizon_config_enable_ha_router: true
horizon_config_multidomain_support: true
horizon_config_caching: true
horizon_config_caching_servers:
  - 172.0.0.6:11211
  - 172.0.0.7:11211
  - 172.0.0.8:11211
horizon_config_themes:
  - default
horizon_config_secret_key: "{{ lookup('password', 'chars=ascii_letters,digits length=64') }}"
```

License
-------
Apache

Author Information
------------------
This role was created in 2016 by Gaëtan Trellu (goldyfruit).
