Ansible dashboard
=======

![CI](https://github.com/sgaduuw/ansible-role-ansiboard/actions/workflows/ci.yml/badge.svg)

AnsiBoard will create an overview of Groups and Host that are members of said groups. All this is based on information about Hosts in the Ansible cache.

The generated dashboard includes:
- A front page with fleet-wide summary charts and group listing
- An all-hosts page with a searchable, sortable table of every host
- Per-group pages with donut charts and searchable, sortable host tables
- Per-host detail pages with a configurable fact table and full JSON dump
- CSV export on all table pages
- Automatic dark mode support

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
| `ansiboard_group_charts` | *(see below)* | Donut charts shown on group and all-hosts pages |
| `ansiboard_group_columns` | *(see below)* | Columns shown in group and all-hosts tables |
| `ansiboard_host_facts` | *(see below)* | List of facts to display on each host page |

All fact keys refer to entries in the `ansible_facts` dictionary (without the `ansible_` prefix).

`ansiboard_group_charts` defines donut charts rendered above the host tables. Each entry produces a chart showing the distribution of values for a given fact. Set to `[]` to disable charts.

```yaml
ansiboard_group_charts:
  - label: Distribution
    key: distribution
  - label: Kernel
    key: kernel
  - label: Virtualization
    key: virtualization_role
```

`ansiboard_group_columns` defines the columns shown in host tables. Each entry has a `label` and a `key`. An optional `format` field supports computed values:

| Format | Description |
|--------|-------------|
| `cores` | Shows `processor_cores`/`processor_vcpus` |
| `mem_gib` | Converts `memtotal_mb` to GiB |
| `ipv4` | Extracts `address` from the `default_ipv4` dict |
| `uptime` | Converts `uptime_seconds` to human-readable (43d, 5h, 15m) |
| `facts_age` | Computes time since facts were gathered, highlights stale entries |

Tables support search, sorting, pagination, and CSV export via Grid.js.

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

No Ansible role dependencies. The generated dashboard loads [Grid.js](https://gridjs.io/) from cdn.jsdelivr.net for table search, sorting, pagination, and CSV export. Browsers viewing the dashboard need internet access.

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
