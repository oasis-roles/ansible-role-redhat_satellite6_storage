[![Build Status](https://travis-ci.com/oasis-roles/ansible-role-redhat_satellite6_storage.svg?branch=master)](https://travis-ci.com/oasis-roles/ansible-role-redhat_satellite6_storage)

# satellite\_storage

An Ansible role for configuring logical volumes and mount points for Red Hat Satellite Server(s) and Capsule(s). Of course, it's also compatible with Foreman and Katello.

## Requirements

- Ansible 2.8 or higher
- Red Hat Enterprise Linux 7 or CentOS 7
- Valid Red Hat Subscriptions

## Warning
This role doesn't pay attention if the mount points already exist or not, it will just blindly try to create them.

## Role Variables

Currently the following variables are supported:

### General
* `satellite_storage_become` - Default: true. If this role needs administrator
  privileges, then use the Ansible become functionality (based off sudo).
* `satellite_storage_become_user` - Default: root. If the role uses the become
  functionality for privilege escalation, then this is the name of the target
  user to change to.

### Storage settings
See [Satellite 6 Installation Guide - 1.2.1. Storage Requirements](https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html-single/installing_satellite_server_from_a_connected_network/index#storage_requirements) for details on sizing.

| Parameter                      | Default         | Description
|--------------------------------|-----------------|------------
| `satellite_pvs`                | `/dev/sdb`      | Physical volume(s) to use for Satellite storage
| `satellite_vg`                 | `satellite_vg` | Volume group to use or create for Satellite storage
| `satellite_type`               | `master`        | One of ['master', 'capsule'] to configure the storage for the given type
| `satellite_lv_pulp_cache_size` | `20g`           | Initial size of the `/var/cache/pulp` volume
| `satellite_lv_pulp_size`       | `500g`          | Initial size of the `/var/lib/pulp` volume
| `satellite_lv_mongodb_size`    | `50g`           | Initial size of the `/var/lib/mongodb` volume
| `satellite_lv_qpidd_size`      | `10g`           | Initial size of the `/var/lib/qpidd` volume. The `/var/lib/qpidd` directory uses slightly more than 2 MB per Content Host managed by the `goferd` service. For example, 10 000 Content Hosts require 20 GB of disk space
| `satellite_lv_pgsql_size`      | `10g`           | Initial size of the `/var/lib/pgsql` volume
| `satellite_lv_squid_size`      | `10g`           | Initial size of the `/var/spool/squid` volume
| `satellite_lv_puppetlabs_size` | `0g`            | Initial size of the `/opt/puppetlabs` volume
| `satellite_lv_puppet_size`     | `0g`            | Initial size of the `/etc/puppet/environment` volume

### `satellite_type`
List of the mounts that get created depending on the specified Satellite type:

| Mountpoint                | Master | Capsule | Description |
| ------------------------- | ------ | ------- | ----------- |
| `/var/cache/pulp`         | x      | x       | Pulp package cache |
| `/var/lib/pulp`           | x      | x       | Pulp package storage |
| `/var/lib/mongodb`        | x      | x       | Pulp database backend |
| `/opt/puppetlabs`         | x      | x       | Puppet installation |
| `/etc/puppet/environment` | x      | x       | Puppet environment files |
| `/var/lib/qpidd`          | x      |         | Goferd content host cache |
| `/var/lib/pgsql`          | x      |         | Foreman database |
| `/var/spool/squid`        | x      |         | Squid proxy |

## Dependencies
None

## Example Playbook

Simple example:
```yaml
- hosts: satellite-servers
  roles:
    - role: oasis_roles.satellite_storage
```

Additional example with different hard disk and pulp size:
```yaml
- hosts: satellite-servers
  roles:
    - role: oasis_roles.satellite_storage
      satellite_pvs: /dev/vdb
      satellite_lv_pulp_size: 800g
```

## License
GPLv3

## Contributors
- [Ian Tewksbury](https://github.com/itewk)
- [Christian Stankowic](https://github.com/stdevel)
