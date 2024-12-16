PostgreSQL role
=========

Role that installs PostgreSQL.

Role Variables
--------------
postgresql_version: 16
postgresql_data_directory: "/var/lib/postgresql/{{ postgresql_version }}/main"

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: postgres }

License
-------

MIT
