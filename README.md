Role Name
=========

Installs MySQL 5.7 and 8.0 on CentOS 7 and FreeBSD

Current functionality:  
* Install MySQL 5.7 or 8.0 from repo (PKG or community repo)
* Place InnoDB data and logs in other locations (for example ZFS datasets)  
* Setup my.cnf  
* Changes the random MySQL root password to the supplied mysql\_rootpw
* Sets auto login for selected users by writing a .my.cnf in their home.  
* Installs Python-mysql
* Creates databases from mysql\_databases
* Optionally loads the sql file listed under the db variable when the DB is first created.   
* Creates users from mysql\_users


Requirements
------------

Tested on CentOS 7 with community MySQL 5.7 and 8.0
Tested on FreeBSD 12 with community MySQL 8.0

Role Variables
--------------
TODO: Many more

```
mysql_databases: []
```

A list of databases to create minimum is a name (defaults to encoding: utf8 and collation: utf8_general_ci)  
When specifying _backup\_file_ you can load a sql file to be restored when the DB is first created. Such file needs to be in _freebsd\_mysql\_backup\_location_

Example:  

```
mysql_databases:
- name: db1
- name: db2
  backup_file: db2.sql
- name: latin_db
  encoding: latin1
  collation: latin1_general_ci
```

```
mysql_users: []
```
A list of database users and their privileges. Mirrors the mysql_user module in Ansible.  

Example:  

```
mysql_users:
    - name: example_user
      host: "%"
      password: DatSecurityDoh
      priv: "db1.*:ALL/latin_db.*:ALL"
```

Dumping databases
-----------------

Databases can be dumped using the dump.yml task.
For example:

```
- include_role:
    name: coffeesprout.mysql
    tasks_from: dump  
  loop:
  - database1
  - database2
  loop_control:
    loop_var: mysql_database
```
The defaults will dump to /tmp/mysql on the target machine.
Optionally setting: mysql_fetch_dumps will retrieve the dumps and copy them to {{ playbook_dir }}/files/mysql/{{ mysql_database }}.xz

Customize below:
```
mysql_dump_folder: /tmp/mysql
mysql_dump_compression: "xz"
mysql_dump_fetch_destination: "{{ playbook_dir }}/files/mysql/"
```
Dependencies
------------


Example Playbook
----------------

Example 1 (default latest MySQL)

    - hosts: mysql
  	  user: support
  	  become: yes
  	  become_user: root
  	  vars:
        mysql_rootpw: SomeLamePassword1!
        mysql_adminusers:
    	  - "/home/support"
    	  mysql_databases:
         - name: db1
         - name: db2
         - name: latin_db
           encoding: latin1
           collation: latin1_general_ci
    	  mysql_users:
    	  - name: example_user
    	    host: "%"
    	    password: 0000
    	    priv: "db1.*:ALL/latin_db.*:ALL"
      roles:
      - coffeesprout.mysql

License
-------

BSD

Author Information
------------------

