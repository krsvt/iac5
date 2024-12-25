# Ansible Collection - iac.lab5
Коллекция с ролью postgres и тестированием через Molecule (как в [официальном мануале Molecule](https://ansible.readthedocs.io/projects/molecule/getting-started/)).

## Использование
```
ansible-playbook playbooks/playbook.yaml -i inventory.ini
```

## Как поднимается кластер

1. Обе СУБД поднимаются с помощью systemd, PGDATA копируются в указанные директории (roles/postgres/tasks/main.yml).
2. Определение master и replic происходит с помощью переменной:
```
pg_master_or_replica:
  - hostname: srv1
    role: master
  - hostname: srv2
    role: replica
```

Здесь srv1, srv2 - inventory_hostname машин.

3. В мастере - создается роль для реплики, разрешается доступ реплики в pg_hba, в postgresql.conf - стандартные настройки для репликации.

4. В реплике - аналогично. Стоит заметить, что pg_basebackup ожидает работающий мастер, поэтому реплика должна стартовать второй.


### Master sender
```
vagrant@srv1:~$ ps -eaf | grep sender
postgres    8842    5651  0 09:56 ?        00:00:00 postgres: 14/main: walsender replica 192.168.56.202(55572) streaming 0/5000060
vagrant     8860    5725  0 09:57 pts/0    00:00:00 grep --color=auto sender
```
### Replica receiver
```
vagrant@srv2:~$ ps -eaf | grep receiver
postgres    8342    8337  0 09:56 ?        00:00:00 postgres: 14/main: walreceiver streaming 0/5000060
vagrant     8375    5593  0 09:57 pts/0    00:00:00 grep --color=auto receiver
vagrant@srv2:~$ ps -eaf | grep startup
root         698       1  0 09:36 ?        00:00:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
root         865       1  0 09:36 ?        00:00:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
postgres    8338    8337  0 09:56 ?        00:00:00 postgres: 14/main: startup recovering 000000010000000000000005
vagrant     8377    5593  0 09:57 pts/0    00:00:00 grep --color=auto startup
```
