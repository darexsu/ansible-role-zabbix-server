# Ansible role Zabbix-server 

[![CI Molecule](https://github.com/darexsu/ansible-role-zabbix-server/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-zabbix-server/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57603?color=blue&label=downloads)

|  Testing         |  Debian            |  Ubuntu         |  Rocky Linux  | Oracle Linux |
| :--------------: | :----------------: | :-------------: | :-----------: | :----------: |
| Distro release   |  10, 11            | 18.04, 20.04    |       8       |      8       |
| Zabbix version   |  6.0 LTS           |   6.0 LTS       |    6.0 LTS    |  6.0 LTS     |             
                                          
Zabbix Roles: [Zabbix-server](https://github.com/darexsu/ansible-role-zabbix-server/)[Zabbix-agent](https://github.com/darexsu/ansible-role-zabbix-agent/), [Zabbix-gui](https://github.com/darexsu/ansible-role-zabbix-gui/)                                          

### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.nginx --force
```
### 2) Example playbooks: 
  
  - [full playbook Zabbix-server, MariaDB](#full-playbook-zabbix-server-mariadb-php-nginx)  
    - install
      - [zabbix-server](#install-zabbix-server)
      - [Zabbix-server, MariaDB, from distro repo](#install-zabbix-gui) 
    - config
      - [zabbix_server.conf](#configure-zabbix-server-conf)

Replace dictionary and Merge dictionary (with "hash_behaviour=replace" in ansible.cfg):
```
[host_vars]           [host_vars]
---                   ---
  vars:                 vars:
    dict:                 merge:  <-- # Enable Merge
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"
```
Role recursive merge:
```
[host_vars]     [current role]    [include_role]
  
  dict:          dict:              dict:
    a: "1" -->     a: "1"    -->      a: "1"
                   b: "2"    -->      b: "2"
                                      c: "3"
    
```
##### Full playbook (Zabbix-server, MariaDB, PHP, Nginx)
```yaml
- name: Converge
  hosts: all
  become: true

  vars:
    merge:
# Zabbix-server --- 
      zabbix_server:
        enabled: true
        version: "6.0"
        server_name: "Zabbix server"  
        db_type: "MYSQL"
        db_port: "3306"
        db_name: "zabbix"
        db_user: "zabbix"
        db_password: "change_me"
      zabbix_server_install:
        enabled: true
      zabbix_server_repo:
        enabled: true
      zabbix_server_conf:
        enabled: true
      zabbix_server_gui:
        enabled: true
# MariaDB ---------
      mariadb:
        enabled: true
        version: "10.5"
      mariadb_repo:
        enabled: true
      mariadb_install:
        enabled: true
      mariadb_database:
        zabbix_server:
          enabled: true
          remove_old: true
      mariadb_user:
        zabbix_server:
          enabled: true
      mariadb_sql:
        zabbix_server:
          schema: true

  tasks:
  - name: include role darexsu.zabbix_server
    include_role: 
      name: darexsu.zabbix_server

```

##### Install Zabbix-server

```yaml
- name: Converge
  hosts: all
  become: true

  vars:
    merge:
# Zabbix-server ----
      zabbix_server:
        enabled: true
        version: "6.0"
        server_name: "Zabbix server"  
        db_type: "MYSQL"
        db_port: "3306"
        db_name: "zabbix"
        db_user: "zabbix"
        db_password: "change_me"
      zabbix_server_install:
        enabled: true
      zabbix_server_repo:
        enabled: true
      zabbix_server_conf:
        enabled: true
      zabbix_server_gui:
        enabled: true

  tasks:
  - name: include role darexsu.zabbix_server
    include_role: 
      name: darexsu.zabbix_server

```
##### Zabbix-server from distro
```yaml
- name: Converge
  hosts: all
  become: true

  vars:
    merge:
# Zabbix-server ----
      zabbix_server:
        enabled: true
        version: "6.0"
        server_name: "Zabbix server"  
        db_type: "MYSQL"
        db_port: "3306"
        db_name: "zabbix"
        db_user: "zabbix"
        db_password: "change_me"
      zabbix_server_install:
        enabled: true
      zabbix_server_repo:
        enabled: true
      zabbix_server_conf:
        enabled: true
      zabbix_server_gui:
        enabled: true
 # MariaDB ---------
      mariadb:
        enabled: true
        version: "10.5"
      mariadb_repo:
        enabled: false             # <-- disable third-party repo
      mariadb_install:
        enabled: true
      mariadb_database:
        zabbix_server:
          enabled: true
          remove_old: true
      mariadb_user:
        zabbix_server:
          enabled: true
      mariadb_sql:
        zabbix_server:
          schema: true

  tasks:
  - name: include role darexsu.zabbix_server
    include_role: 
      name: darexsu.zabbix_server

```
##### Configure zabbix-server.conf
```yaml
- name: Converge
  hosts: all
  become: true

  vars:
    merge:
# Zabbix-server -----
      zabbix_server:                   
        enabled: true
      zabbix_server_conf:
        enabled: true
      zabbix_server_conf:
        enabled: true
        backup: true
        vars:
          ListenPort: "10051"
          LogFile: "/var/log/zabbix/zabbix_server.log"
          LogFileSize: "0"
          PidFile: "/var/run/zabbix/zabbix_server.pid"
          SocketDir: "/var/run/zabbix"
        # ...
 
  tasks:
  - name: include role darexsu.zabbix_server
    include_role: 
      name: darexsu.zabbix_server

```