## percona-server-tools

[![Build Status](https://travis-ci.org/Oefenweb/ansible-percona-server-tools.svg?branch=master)](https://travis-ci.org/Oefenweb/ansible-percona-server-tools) [![Ansible Galaxy](http://img.shields.io/badge/ansible--galaxy-percona--server--tools-blue.svg)](https://galaxy.ansible.com/list#/roles/5042)

Manage [percona-server](https://www.percona.com/software/mysql-database/percona-server) server (or any other flavour of MySQL) in Debian-like systems.

#### Requirements

* `mysql` (will not be installed)
* `mysqld`, version `>= 5.5` (will not be installed)

#### Variables

##### Reset root password

Uses the `debian-sys-maint` from `/etc/mysql/debian.cnf` to login with.

* `percona_server_tools_reset_root_password`: [default: `{}`]:
* `percona_server_tools_reset_root_password.run`: [default: `false`]: Whether or not to run `reset-root-password.yml`

* `percona_server_tools_reset_root_password.root_password`: [required]: Root password

##### Resize InnoDB logs

Removes the current InnoDB log files in a safe way.

* `percona_server_tools_reset_ib_logfile`: [default: `{}`]:
* `percona_server_tools_reset_ib_logfile.run`: [default: `false`]: Whether or not to run `reset-ib-logfile.yml`

* `percona_server_tools_reset_ib_logfile.ib_logfiles`: [optional, default: `[/var/lib/mysql/ib_logfile0, /var/lib/mysql/ib_logfile1]`]: InnoDB logs to resize

##### Setup slave replication (using `xtrabackup`)

Sets up slave replication on one or more slaves from a healthy master. Assumes that the slaves are able to pull backups using `rsync` over `ssh`, so ssh (keys) need to be set up.

* `percona_server_tools_setup_slave_replication`: [default: `{}`]:
* `percona_server_tools_setup_slave_replication.run`: [default: `false`]: Whether or not to run `setup-slave-replication.yml`

* `percona_server_tools_setup_slave_replication.inventory`: [required]: Inventory section
* `percona_server_tools_setup_slave_replication.inventory.master`: [required]: The inventory hostname of the master server (e.g. `db-01.example.com`)
* `percona_server_tools_setup_slave_replication.inventory.slaves`: [required]: The inventory hostname(s) of the slave server(s) (e.g. `[db-02.example.com, db-03.example.com]`)

* `percona_server_tools_setup_slave_replication.innobackupex`: [required]: Innobackupex section
* `percona_server_tools_setup_slave_replication.innobackupex.user`: [optional]: Specifies the user (i.e., the MySQL username used when connecting to the server) to login as, if that’s not the current user. It is passed to the `mysql` child process without alteration
* `percona_server_tools_setup_slave_replication.innobackupex.password`: [optional]: Specifies the password to use when connecting to the database. It is passed to the `mysql` child process without alteration
* `percona_server_tools_setup_slave_replication.innobackupex.parallel`: [optional]: Specifies the number of threads the `xtrabackup` child process should use to back up files concurrently
* `percona_server_tools_setup_slave_replication.innobackupex.rsync`: [optional]: Use the `rsync` utility to optimize local file transfers. When this option is specified, `innobackupex` uses `rsync` to copy all non-InnoDB files instead of spawning a separate `cp` for each file, which can be much faster for servers with a large number of databases or tables
* `percona_server_tools_setup_slave_replication.innobackupex.backup_dir`: [required]: Specifies the backup directory
* `percona_server_tools_setup_slave_replication.innobackupex.use_memory`: [optional]: Specifies the amount of memory in bytes for `xtrabackup` to use for crash recovery while preparing a backup

* `percona_server_tools_setup_slave_replication.master`: [required]: Master section
* `percona_server_tools_setup_slave_replication.master.host`: [required]: Specifies the `MASTER_HOST`, needed to setup the replication, but also the pull backups from the master (`rsync` over `ssh`) (e.g. `{{ hostvars['db-01.example.com']['ansible_eth1']['ipv4']['address'] }}`)
* `percona_server_tools_setup_slave_replication.master.user`: [required]: Specifies the `MASTER_USER` (e.g. `replicator`)
* `percona_server_tools_setup_slave_replication.master.password`: [required]: Specifies the `MASTER_PASSWORD`

##### Setup master replication (using `xtrabackup`)

Sets up master replication on one master from a healthy master. Assumes that the master is able to pull backups using `rsync` over `ssh`, so ssh (keys) need to be set up.

* `percona_server_tools_setup_master_replication.run`: [default: `false`]: Whether or not to run `setup-master-replication.yml`

* `percona_server_tools_setup_master_replication.inventory.master1`: [required]: The inventory hostname of the (primary) master server (e.g. `db-01.example.com`)
* `percona_server_tools_setup_master_replication.inventory.master2`: [required]: The inventory hostname of the (secondary) master server (e.g. `db-02.example.com`)

* `percona_server_tools_setup_master_replication.innobackupex.user`: [optional]: Specifies the user (i.e., the MySQL username used when connecting to the server) to login as, if that’s not the current user. It is passed to the `mysql` child process without alteration
* `percona_server_tools_setup_master_replication.innobackupex.password`: [optional]: Specifies the password to use when connecting to the database. It is passed to the `mysql` child process without alteration
* `percona_server_tools_setup_master_replication.innobackupex.parallel`: [optional]: Specifies the number of threads the `xtrabackup` child process should use to back up files concurrently
* `percona_server_tools_setup_master_replication.innobackupex.rsync`: [optional]: Use the `rsync` utility to optimize local file transfers. When this option is specified, `innobackupex` uses `rsync` to copy all non-InnoDB files instead of spawning a separate `cp` for each file, which can be much faster for servers with a large number of databases or tables
* `percona_server_tools_setup_master_replication.innobackupex.backup_dir`: [required]: Specifies the backup directory
* `percona_server_tools_setup_master_replication.innobackupex.use_memory`: [optional]: Specifies the amount of memory in bytes for `xtrabackup` to use for crash recovery while preparing a backup

* `percona_server_tools_setup_master_replication.master1.host`: [required]: Specifies the `MASTER_HOST` (on `master2`), needed to setup the replication, but also the pull backups from the master (`rsync` over `ssh`) (e.g. `{{ hostvars[percona_server_tools_setup_slave_replication_master]['ansible_eth1']['ipv4']['address'] }}`)
* `percona_server_tools_setup_master_replication.master1.user`: [required]: Specifies the `MASTER_USER` (e.g. `replicator`)
* `percona_server_tools_setup_master_replication.master1.password`: [required]: Specifies the `MASTER_PASSWORD`
* `percona_server_tools_setup_master_replication.master2.host`: [required]: Specifies the `MASTER_HOST` (on `master1`), needed to setup the replication, but also the pull backups from the master (`rsync` over `ssh`) (e.g. `{{ hostvars[percona_server_tools_setup_slave_replication_master]['ansible_eth1']['ipv4']['address'] }}`)
* `percona_server_tools_setup_master_replication.master2.user`: [optional, default: `master1.user`]: Same as above
* `percona_server_tools_setup_master_replication.master2.password`: [optional, default: `master1.password`]: Same as above

## Dependencies

None

## Recommended

* `percona-client` ([see](https://github.com/Oefenweb/ansible-percona-client))
* `percona-server` ([see](https://github.com/Oefenweb/ansible-percona-server))
* `ssh-keys` ([see](https://github.com/Oefenweb/ansible-ssh-keys))

#### Example(s)

##### Reset root password

```yaml
---
- hosts: all
  roles:
    - percona-server-tools
  vars:
    percona_server_tools_reset_root_password:
      run: true
      root_password: '6j~14F(Um~@nAz4hn6dT'
```

##### Resize InnoDB logs

```yaml
---
- hosts: all
  roles:
    - percona-server-tools
  vars:
    percona_server_tools_reset_ib_logfile:
      run: true
```

##### Setup slave replication

```yaml
---
- hosts: all
  roles:
    - percona-server-tools
  vars:
    percona_server_tools_setup_slave_replication:
      run: true
      inventory:
        master: db-01.example.com
        slaves:
          - db-02.example.com
    
      backup_dir: /tmp/xtrabackup
    
      master:
        host: "{{ hostvars[percona_server_tools_setup_slave_replication_master]['ansible_eth1']['ipv4']['address'] }}"
        user: replicator
        password: 'Z$8>YM"KUVRv6sW#=O-A'
```

#### License

MIT

#### Author Information

Mischa ter Smitten

#### Feedback, bug-reports, requests, ...

Are [welcome](https://github.com/Oefenweb/ansible-percona-server-tools/issues)!
