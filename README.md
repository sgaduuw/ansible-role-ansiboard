Ansible dashboard
=======

![CI](https://github.com/sgaduuw/ansible-role-ansiboard/actions/workflows/ci.yml/badge.svg)

AnsiBoard will create an overview of Groups and Host that are members of said groups. All this is based on information about Hosts in the Ansible cache.

Requirements
------------

Caching of facts needs to be enabled. For example, use any one of the following options:
```
fact_caching = yaml
fact_caching = json
fact_caching = redis
```

Role Variables
--------------

| Variable | Default | Description |
|----------|---------|-------------|
| `ansiboard_basedir` | `/var/www/html/ansiboard` | Base directory for the generated HTML dashboard |
| `ansiboard_group_charts` | *(see below)* | Donut charts shown on group pages |
| `ansiboard_group_columns` | *(see below)* | Columns shown in the group overview table |
| `ansiboard_host_facts` | *(see below)* | List of facts to display on each host page |

All fact keys refer to entries in the `ansible_facts` dictionary (without the `ansible_` prefix).

`ansiboard_group_charts` defines donut charts rendered above the host table on each group page. Each entry produces a chart showing the distribution of values for a given fact across all hosts in the group. Set to `[]` to disable charts.

```yaml
ansiboard_group_charts:
  - label: Distribution
    key: distribution
  - label: Architecture
    key: architecture
  - label: OS family
    key: os_family
```

`ansiboard_group_columns` defines the columns shown in the group member table. Each entry has a `label` and a `key`. An optional `format` field supports computed values: `cores` shows physical cores/vcpus, and `mem_gib` converts `memtotal_mb` to GiB. The defaults produce a table like:

| Hostname  | Distro    | Version | Kernel               | Arch    | Cores | Mem (GiB) |
|-----------|-----------|---------|----------------------|---------|-------|-----------|
| coruscant | Archlinux | N/A     | 6.19.11-arch1-1      | x86_64  | 8/16  | 128       |
| endor     | Debian    | 13.2    | 6.12.47+rpt-rpi-2712 | aarch64 | 4     | 4         |

`ansiboard_host_facts` defines the fact table on individual host pages. Override any variable to show whichever facts are relevant to your environment:

```yaml
ansiboard_host_facts:
  - label: Hostname
    key: hostname
  - label: Distribution
    key: distribution
  - label: Kernel
    key: kernel
```

The full JSON of all gathered facts is always available in a collapsible section at the bottom of each host page.

Dependencies
------------

None.

Example Playbook
----------------

Ansiboard relies heavily on cached facts. To make sure all facts are up-to-date, run the setup module against all hosts in the inventory before the ansiboard role.

    - hosts: all
      gather_facts: true
      tasks:
        - name: Gather facts
          ansible.builtin.setup:

    - hosts: localhost
      roles:
        - role: ansiboard
          tags: dashboard
          vars:
            ansiboard_basedir: /home/sgaduuw/tmp

License
-------

MIT

Author Information
------------------

Created by Eelco Wesemann (Sgaduuw), 2020
