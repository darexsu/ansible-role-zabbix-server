# Ansible role Zabbix-server 

[![CI Molecule](https://github.com/darexsu/ansible-role-zabbix-server/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-zabbix-server/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/58157?color=blue&label=downloads)

  - Role:
      - [platforms](#platforms)
      - [install](#install)
      - [requirements](#requirements)
      - [relative](#relative)
      - [merge behaviour](#merge-behaviour)
  - Playbooks (merge version):
      - [install and configure: Zabbix-server, MariaDB, FirewallD](#install-and-configure-zabbix-server-mariadb-merge-version)
      - [install and configure: Zabbix-server, MySQL, FirewallD](#install-and-configure-zabbix-server-mysql-merge-version)
        - [install: zabbix-server](#install-zabbix-server-merge-version)
        - [configure: zabbix_server.conf](#configure-zabbix_serverconf-merge-version)  
  - Playbooks (full version):
      - [install and configure: Zabbix-server, MariaDB, FirewallD](#install-and-configure-zabbix-server-mariadb-full-version)
      - [install and configure: Zabbix-server, MySQL, FirewallD](#install-and-configure-zabbix-server-mysql-full-version)
        - [install: zabbix-server](#install-zabbix-server-full-version)
        - [configure: zabbix_server.conf](#configure-zabbix_serverconf-full-version)

### Platforms

|  Testing         |  repo: zabbix      |
| :--------------: | :----------------: |
| Debian 11        |  Zabbix  6.0 lts   |
| Debian 10        |  Zabbix  6.0 lts   |
| Ubuntu 20.04     |  Zabbix  6.0 lts   |
| Ubuntu 18.04     |  Zabbix  6.0 lts   |
| Oracle Linux 8   |  Zabbix  6.0 lts   |
| Rocky Linux 8    |  Zabbix  6.0 lts   |

### Install
```
ansible-galaxy install darexsu.zabbix_server --force

```
### Requirements
collections: [ansible.posix](https://docs.ansible.com/ansible/latest/collections/ansible/posix/index.html), [community.mysql](https://docs.ansible.com/ansible/latest/collections/community/mysql/index.html#plugins-in-community-mysql)

roles: [MariaDB](https://github.com/darexsu/ansible-role-mariadb), [MySQL](https://github.com/darexsu/ansible-role-mysql), [FirewallD](https://github.com/darexsu/ansible-role-firewalld) (will automatically be installed)

### Relative

Another Zabbix roles: [Zabbix-server](https://github.com/darexsu/ansible-role-zabbix-server/), [Zabbix-agent](https://github.com/darexsu/ansible-role-zabbix-agent/), [Zabbix-gui](https://github.com/darexsu/ansible-role-zabbix-gui/)                                       

### Merge behaviour
Replace or Merge dictionaries (with "hash_behaviour=replace" in ansible.cfg):
```
# Replace             # Merge
---                   ---
  vars:                 vars:
    dict:                 merge:
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"

# How does merge work?
Your vars [host_vars]  -->  default vars [current role] --> default vars [include role]
  
  dict:          dict:              dict:
    a: "1" -->     a: "1"    -->      a: "1"
                   b: "2"    -->      b: "2"
                                      c: "3"
    
```
##### Install and configure: Zabbix-server, MariaDB (merge version)
```yaml
- hosts: all
  become: true

  vars:
    merge:
      # Zabbix_server
      zabbix_server:
        enabled: true
        version: "6.0"
        server_name: "Zabbix server"
        db_type: "MYSQL"
        db_port: "3306"
        db_host: "localhost"
        db_name: "zabbix"
        db_user: "zabbix"
        db_password: "change_me"
      # Zabbix_server -> install
      zabbix_server_install:
        enabled: true
      # Zabbix_server -> config
      zabbix_server_conf:
        enabled: true

      # MariaDB
      mariadb:
        enabled: true
        repo: "mariadb"
        version: "10.5"
      # MariaDB -> install
      mariadb_install:
        enabled: true
      # MariaDB -> database
      mariadb_database:
        zabbix_server:
          enabled: true
      # MariaDB -> user
      mariadb_user:
        zabbix_server:
          enabled: true
      # MariaDB -> sql
      mariadb_sql:
        zabbix_server:
          schema: true

      # FirewallD
      firewalld:
        enabled: true
      # FirewallD -> rules
      firewalld_rules:
        zabbix_port_10050:
          enabled: true
        zabbix_port_10051:
          enabled: true
        mysql_port_3306:
          enabled: true

  tasks:
  - name: include role darexsu.zabbix_server
    include_role: 
      name: darexsu.zabbix_server

```
##### Install and configure: Zabbix-server, MySQL (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Zabbix_server
      zabbix_server:
        enabled: true
        repo: "zabbix"
        version: "6.0"
        server_name: "Zabbix server"
        db_type: "MYSQL"
        db_port: "3306"
        db_host: "localhost"
        db_name: "zabbix"
        db_user: "zabbix"
        db_password: "change_me"
      # Zabbix_server -> install
      zabbix_server_install:
        enabled: true
      # Zabbix_server -> config
      zabbix_server_conf:
        enabled: true

      # MySQL
      mysql:
        enabled: true
        repo: "mysql"
        version: "8.0"
      # MySQL -> install
      mysql_install:
        enabled: true
      # MySQL -> database
      mysql_database:
        zabbix_server:
          enabled: true
      # MySQL -> user
      mysql_user:
        zabbix_server:
          enabled: true
      # MySQL -> sql
      mysql_sql:
        zabbix_server:
          schema: true
      
      # FirewallD
      firewalld:
        enabled: true
      # FirewallD -> rules
      firewalld_rules:
        zabbix_port_10050:
          enabled: true
        zabbix_port_10051:
          enabled: true
        mysql_port_3306:
          enabled: true

  tasks:
    - name: include role darexsu.zabbix_server
      include_role:
        name: darexsu.zabbix_server
```
##### Install: zabbix-server (merge version)

```yaml
- hosts: all
  become: true

  vars:
    merge:
      # Zabbix_server
      zabbix_server:
        enabled: true
        version: "6.0"
        server_name: "Zabbix server"
        db_type: "MYSQL"
        db_port: "3306"
        db_host: "localhost"
        db_name: "zabbix"
        db_user: "zabbix"
        db_password: "change_me"
      # Zabbix_server -> install
      zabbix_server_install:
        enabled: true
      # Zabbix_server -> config
      zabbix_server_conf:
        enabled: true

  tasks:
  - name: include role darexsu.zabbix_server
    include_role: 
      name: darexsu.zabbix_server

```

##### Configure: zabbix_server.conf (merge version)
```yaml
- hosts: all
  become: true

  vars:
    merge:
      # Zabbix_server
      zabbix_server:
        enabled: true
        server_name: "Zabbix server"
        db_type: "MYSQL"
        db_port: "3306"
        db_host: "localhost"
        db_name: "zabbix"
        db_user: "zabbix"
        db_password: "change_me"
      # Zabbix_server -> config
      zabbix_server_conf:
        enabled: true
        data:
          ListenPort: "10051"
          LogFile: "/var/log/zabbix/zabbix_server.log"
          LogFileSize: "0"
          PidFile: "/var/run/zabbix/zabbix_server.pid"
          SocketDir: "/var/run/zabbix"
          DBHost: "{{ zabbix_server.db_host }}"
          DBName: "{{ zabbix_server.db_name }}"
          DBSchema: ""
          DBUser: "{{ zabbix_server.db_user }}"
          DBPassword: "{{ zabbix_server.db_password }}"
          DBSocket: ""
          DBPort: "{{ zabbix_server.db_port }}"
          SNMPTrapperFile: "/var/log/snmptrap/snmptrap.log"
          CacheUpdateFrequency: "60"
          Timeout: "4"
          AlertScriptsPath: "/usr/lib/zabbix/alertscripts"
          ExternalScripts: "/usr/lib/zabbix/externalscripts"
          FpingLocation: "/usr/bin/fping"
          Fping6Location: "/usr/bin/fping6"
          LogSlowQueries: "3000"
        # ...
 
  tasks:
  - name: include role darexsu.zabbix_server
    include_role: 
      name: darexsu.zabbix_server

```
##### Install and configure: Zabbix-server, MariaDB (full version)
```yaml
- hosts: all
  become: true

  vars:
    # Zabbix_server
    zabbix_server:
      enabled: true
      repo: "zabbix"
      version: "6.0"
      server_name: "Zabbix server"
      db_type: "MYSQL"
      db_port: "3306"
      db_host: "localhost"
      db_name: "zabbix"
      db_user: "zabbix"
      db_password: "change_me"
      service:
        state: "started"
        enabled: true
    # Zabbix_server -> install
    zabbix_server_install:
      enabled: true
      packages:
        Debian: [zabbix-server-mysql, zabbix-agent, zabbix-sql-scripts]
        RedHat: [zabbix-server-mysql, zabbix-agent, zabbix-sql-scripts]
      dependencies:
        Debian: [apt-transport-https, ca-certificates, gnupg2, lsb-release]
        RedHat: []
    # Zabbix_server -> config
    zabbix_server_conf:
      enabled: true
      file: "zabbix_server.conf"
      src: "zabbix_server_conf.j2"
      backup: false
      data:
        ListenPort: "10051"
        LogFile: "/var/log/zabbix/zabbix_server.log"
        LogFileSize: "0"
        PidFile: "/var/run/zabbix/zabbix_server.pid"
        SocketDir: "/var/run/zabbix"
        DBHost: "{{ zabbix_server.db_host }}"
        DBName: "{{ zabbix_server.db_name }}"
        DBSchema: ""
        DBUser: "{{ zabbix_server.db_user }}"
        DBPassword: "{{ zabbix_server.db_password }}"
        DBSocket: ""
        DBPort: "{{ zabbix_server.db_port }}"
        SNMPTrapperFile: "/var/log/snmptrap/snmptrap.log"
        CacheUpdateFrequency: "60"
        Timeout: "4"
        AlertScriptsPath: "/usr/lib/zabbix/alertscripts"
        ExternalScripts: "/usr/lib/zabbix/externalscripts"
        FpingLocation: "/usr/bin/fping"
        Fping6Location: "/usr/bin/fping6"
        LogSlowQueries: "3000"

    # MariaDB
    mariadb:
      enabled: true
      repo: "mariadb"
      version: "10.5"
    # MariaDB -> install
    mariadb_install:
      enabled: false
      packages:
        Debian: [mariadb-server]
        RedHat: [mariadb-server]
      dependencies:
        Debian: [gnupg2, python3, python3-pymysql]
        RedHat: [python3, python3-PyMySQL]
    # MariaDB -> database
    mariadb_database:
      zabbix_server:
        enabled: true
        login_user: "root"
        login_password: ""
        login_host: "localhost"
        login_port: "{{ zabbix_server.db_port }}"
        login_unix_socket: "{{ mariadb_const[ansible_os_family]['socket'] }}"
        name: "{{ zabbix_server.db_name }}"
        state: "present"
        target: ""
        encoding: "utf8"
        collation: "utf8_bin"
    # MariaDB -> user
    mariadb_user:
      zabbix_server:
        enabled: true
        login_user: "root"
        login_password: ""
        login_host: "localhost"
        login_port: "{{ zabbix_server.db_port }}"
        login_unix_socket: "{{ mariadb_const[ansible_os_family]['socket'] }}"
        name: "{{ zabbix_server.db_user }}"
        password: "{{ zabbix_server.db_password }}"
        state: "present"
        privileges: 'zabbix.*:ALL'
        encrypted: false
    # MariaDB -> sql
    mariadb_sql:
      zabbix_server:
        schema: true
    
    # FirewallD
    firewalld:
      enabled: true
    # FirewallD -> rules
    firewalld_rules:
      zabbix_port_10050:
        enabled: true
        zone: "public"
        state: "enabled"
        port: "10050/tcp"
        permanent: true
      zabbix_port_10051:
        enabled: true
        zone: "public"
        state: "enabled"
        port: "10051/tcp"
        permanent: true
      mysql_port_3306:
        enabled: true
        zone: "public"
        state: "enabled"
        port: "3306/tcp"
        permanent: true

  tasks:
  - name: include role darexsu.zabbix_server
    include_role: 
      name: darexsu.zabbix_server

```
##### Install and configure: Zabbix-server, MySQL (full version)
```yaml
---
- name: Converge
  hosts: all
  become: true

  pre_tasks:
  - name: Unnessessary command
    ansible.builtin.shell:
      cmd: "{{ lookup('env', 'ANSIBLE_COMMAND') }}"
    when: lookup('env', 'ANSIBLE_COMMAND') | length > 0

  vars:
    # Zabbix_server
    zabbix_server:
      enabled: true
      repo: "zabbix"
      version: "6.0"
      server_name: "Zabbix server"
      db_type: "MYSQL"
      db_port: "3306"
      db_host: "localhost"
      db_name: "zabbix"
      db_user: "zabbix"
      db_password: "change_me"
      service:
        state: "started"
        enabled: true
    # Zabbix_server -> install
    zabbix_server_install:
      enabled: true
      packages:
        Debian: [zabbix-server-mysql, zabbix-agent, zabbix-sql-scripts]
        RedHat: [zabbix-server-mysql, zabbix-agent, zabbix-sql-scripts]
      dependencies:
        Debian: [apt-transport-https, ca-certificates, gnupg2, lsb-release]
        RedHat: []
    # Zabbix_server -> config
    zabbix_server_conf:
      enabled: true
      file: "zabbix_server.conf"
      src: "zabbix_server_conf.j2"
      backup: false
      data:
        ListenPort: "10051"
        LogFile: "/var/log/zabbix/zabbix_server.log"
        LogFileSize: "0"
        PidFile: "/var/run/zabbix/zabbix_server.pid"
        SocketDir: "/var/run/zabbix"
        DBHost: "{{ zabbix_server.db_host }}"
        DBName: "{{ zabbix_server.db_name }}"
        DBSchema: ""
        DBUser: "{{ zabbix_server.db_user }}"
        DBPassword: "{{ zabbix_server.db_password }}"
        DBSocket: ""
        DBPort: "{{ zabbix_server.db_port }}"
        SNMPTrapperFile: "/var/log/snmptrap/snmptrap.log"
        CacheUpdateFrequency: "60"
        Timeout: "4"
        AlertScriptsPath: "/usr/lib/zabbix/alertscripts"
        ExternalScripts: "/usr/lib/zabbix/externalscripts"
        FpingLocation: "/usr/bin/fping"
        Fping6Location: "/usr/bin/fping6"
        LogSlowQueries: "3000"

    # MySQL
    mysql:
      enabled: true
      repo: "mysql"
      version: "8.0"
      service:
        state: "restarted"
        enabled: true
    # MySQL -> install
    mysql_install:
      enabled: true
      packages:
        Debian: [mysql-server]
        RedHat: [mysql-server]
      dependencies:
        Debian: [gnupg2, python3, python3-pymysql]
        RedHat: [python3, python3-PyMySQL]
    # MySQL -> database
    mysql_database:
      zabbix_server:
        enabled: true
        login_user: "root"
        login_password: ""
        login_host: "localhost"
        login_port: "{{ zabbix_server.db_port }}"
        login_unix_socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
        name: "{{ zabbix_server.db_name }}"
        state: "present"
        target: ""
        encoding: "utf8"
        collation: "utf8_bin"
    # MySQL -> user
    mysql_user:
      zabbix_server:
        enabled: true
        login_user: "root"
        login_password: ""
        login_host: "localhost"
        login_port: "{{ zabbix_server.db_port }}"
        login_unix_socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
        name: "{{ zabbix_server.db_user }}"
        password: "{{ zabbix_server.db_password }}"
        state: "present"
        privileges: 'zabbix.*:ALL'
        encrypted: false
    # MySQL -> sql
    mysql_sql:
      zabbix_server:
        schema: true

    # FirewallD
    firewalld:
      enabled: true
    # FirewallD -> rules
    firewalld_rules:
      zabbix_port_10050:
        enabled: true
        zone: "public"
        state: "enabled"
        port: "10050/tcp"
        permanent: true
      zabbix_port_10051:
        enabled: true
        zone: "public"
        state: "enabled"
        port: "10051/tcp"
        permanent: true
      mysql_port_3306:
        enabled: true
        zone: "public"
        state: "enabled"
        port: "3306/tcp"
        permanent: true

  tasks:
    - name: include role darexsu.zabbix_server
      include_role:
        name: darexsu.zabbix_server
```
##### Install: zabbix-server (full version)

```yaml
- name: Converge
  hosts: all
  become: true

  vars:    
    # Zabbix_server
    zabbix_server:
      enabled: true
      repo: "zabbix"
      version: "6.0"
      server_name: "Zabbix server"
      db_type: "MYSQL"
      db_port: "3306"
      db_host: "localhost"
      db_name: "zabbix"
      db_user: "zabbix"
      db_password: "change_me"
      service:
        state: "started"
        enabled: true
    # Zabbix_server -> install
    zabbix_server_install:
      enabled: true
      packages:
        Debian: [zabbix-server-mysql, zabbix-agent, zabbix-sql-scripts]
        RedHat: [zabbix-server-mysql, zabbix-agent, zabbix-sql-scripts]
      dependencies:
        Debian: [apt-transport-https, ca-certificates, gnupg2, lsb-release]
        RedHat: []
    # Zabbix_server -> config
    zabbix_server_conf:
      enabled: true
      file: "zabbix_server.conf"
      src: "zabbix_server_conf.j2"
      backup: false
      data:
        ListenPort: "10051"
        LogFile: "/var/log/zabbix/zabbix_server.log"
        LogFileSize: "0"
        PidFile: "/var/run/zabbix/zabbix_server.pid"
        SocketDir: "/var/run/zabbix"
        DBHost: "{{ zabbix_server.db_host }}"
        DBName: "{{ zabbix_server.db_name }}"
        DBSchema: ""
        DBUser: "{{ zabbix_server.db_user }}"
        DBPassword: "{{ zabbix_server.db_password }}"
        DBSocket: ""
        DBPort: "{{ zabbix_server.db_port }}"
        SNMPTrapperFile: "/var/log/snmptrap/snmptrap.log"
        CacheUpdateFrequency: "60"
        Timeout: "4"
        AlertScriptsPath: "/usr/lib/zabbix/alertscripts"
        ExternalScripts: "/usr/lib/zabbix/externalscripts"
        FpingLocation: "/usr/bin/fping"
        Fping6Location: "/usr/bin/fping6"
        LogSlowQueries: "3000"

  tasks:
  - name: include role darexsu.zabbix_server
    include_role: 
      name: darexsu.zabbix_server

```

##### Configure: zabbix_server.conf (full version)
```yaml
- name: Converge
  hosts: all
  become: true

  vars:
    # Zabbix_server
    zabbix_server:
      enabled: true
      repo: "zabbix"
      version: "6.0"
      server_name: "Zabbix server"
      db_type: "MYSQL"
      db_port: "3306"
      db_host: "localhost"
      db_name: "zabbix"
      db_user: "zabbix"
      db_password: "change_me"
      service:
        state: "started"
        enabled: true
    # Zabbix_server -> config
    zabbix_server_conf:
      enabled: true
      file: "zabbix_server.conf"
      src: "zabbix_server_conf.j2"
      backup: false
      data:
        ListenPort: "10051"
        LogFile: "/var/log/zabbix/zabbix_server.log"
        LogFileSize: "0"
        PidFile: "/var/run/zabbix/zabbix_server.pid"
        SocketDir: "/var/run/zabbix"
        DBHost: "{{ zabbix_server.db_host }}"
        DBName: "{{ zabbix_server.db_name }}"
        DBSchema: ""
        DBUser: "{{ zabbix_server.db_user }}"
        DBPassword: "{{ zabbix_server.db_password }}"
        DBSocket: ""
        DBPort: "{{ zabbix_server.db_port }}"
        SNMPTrapperFile: "/var/log/snmptrap/snmptrap.log"
        CacheUpdateFrequency: "60"
        Timeout: "4"
        AlertScriptsPath: "/usr/lib/zabbix/alertscripts"
        ExternalScripts: "/usr/lib/zabbix/externalscripts"
        FpingLocation: "/usr/bin/fping"
        Fping6Location: "/usr/bin/fping6"
        LogSlowQueries: "3000"
      # ...
 
  tasks:
  - name: include role darexsu.zabbix_server
    include_role: 
      name: darexsu.zabbix_server

```
