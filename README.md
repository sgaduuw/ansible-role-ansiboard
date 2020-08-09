Ansible dashboard
=======

AnsiBoard will create an overview of Groups and Host that are members of said groups. All this is based on information about Hosts in the Ansible cache.

Requirements
------------

Caching of facts needs to be enabled. For example, use any one of the follwing options:
```
fact_caching = yaml
fact_caching = json
fact_caching = redis
```

Role Variables
--------------

Dependencies
------------


Example Playbook
----------------

Ansiboard relies heavily on cached facts. To make sure all facts are up-to-date, run the setup module against all hosts in the inventory before the ansiboard role.

    - hosts: all
      gather_facts: true
      tasks:
        - name: gather facts
          setup:

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
