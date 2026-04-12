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
| `ansiboard_host_facts` | *(see below)* | List of facts to display on each host page |

`ansiboard_host_facts` is a list of dictionaries with `label` and `key` entries. Keys refer to entries in the `ansible_facts` dictionary (without the `ansible_` prefix). The default set covers hostname, domain, distribution, kernel, architecture, IP address, memory, CPU count, and uptime. Override it to display whichever facts are relevant to your environment:

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
