# Ansible Collection - iac.lab4
Коллекция с ролью ovenvpn и тестированием через Molecule (как в [официальном мануале Molecule](https://ansible.readthedocs.io/projects/molecule/getting-started/)).

Установка openvpn с помощью скрипта [openvpn-install](https://github.com/Nyr/openvpn-install)

Сама роль: https://gitlab.com/ansible-roles4162343/ansible-openvpn

## Использование
```
ansible-playbook playbooks/playbook.yaml -i inventory.ini
```
```
vagrant ssh srv1 
```
```
vagrant@srv1:~$ systemctl status openvpn-server@server.service
● openvpn-server@server.service - OpenVPN service for server
     Loaded: loaded (/lib/systemd/system/openvpn-server@.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2024-11-21 19:10:39 UTC; 1min 14s ago
       Docs: man:openvpn(8)
             https://community.openvpn.net/openvpn/wiki/Openvpn24ManPage
             https://community.openvpn.net/openvpn/wiki/HOWTO
   Main PID: 3671 (openvpn)
     Status: "Initialization Sequence Completed"
      Tasks: 1 (limit: 1102)
     Memory: 1.8M
        CPU: 19ms
     CGroup: /system.slice/system-openvpn\x2dserver.slice/openvpn-server@server.service
             └─3671 /usr/sbin/openvpn --status /run/openvpn-server/status-server.log --status-version 2 --suppress-time>
```
```
*[master][~/dev/itmo/iac/iac4]$ openvpn --config client.ovpn/srv1/root/client.ovpn 
2024-11-21 22:15:42 Note: --cipher is not set. OpenVPN versions before 2.5 defaulted to BF-CBC as fallback when cipher negotiation failed in this case. If you need this fallback please add '--data-ciphers-fallback BF-CBC' to your configuration and/or add BF-CBC to --data-ciphers.
2024-11-21 22:15:42 Note: Kernel support for ovpn-dco missing, disabling data channel offload.
2024-11-21 22:15:42 OpenVPN 2.6.12 [git:makepkg/038a94bae57a446c+] x86_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] [DCO] built on Jul 18 2024
2024-11-21 22:15:42 library versions: OpenSSL 3.3.2 3 Sep 2024, LZO 2.10
2024-11-21 22:15:42 DCO version: N/A
2024-11-21 22:15:42 TCP/UDP: Preserving recently used remote address: [AF_INET]5.34.212.87:1194
2024-11-21 22:15:42 Socket Buffers: R=[212992->212992] S=[212992->212992]
2024-11-21 22:15:42 UDPv4 link local: (not bound)
2024-11-21 22:15:42 UDPv4 link remote: [AF_INET]5.34.212.87:1194
```

## Тестирование роли openvpn с помощью molecule
```
[~/dev/itmo/infra/iac/lab4/extensions]$ molecule test
WARNING  Driver vagrant does not provide a schema.
INFO     default scenario test matrix: dependency, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=0...
INFO     Running default > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Populate instance config] ************************************************
ok: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running default > syntax

playbook: /home/svt/dev/itmo/infra/iac/lab4/extensions/molecule/default/converge.yml
INFO     Running default > create

PLAY [Create] ******************************************************************

TASK [Populate instance config dict] *******************************************
skipping: [localhost]

TASK [Convert instance config dict to a list] **********************************
skipping: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=0    changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0

INFO     Running default > prepare

PLAY [Prepare] *****************************************************************

TASK [Gather system info] ******************************************************
ok: [srv1]

TASK [Bootstrap python for Ansible] ********************************************
ok: [srv1]

PLAY RECAP *********************************************************************
srv1                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > converge

PLAY [Converge] ****************************************************************

TASK [Include openvpn role] ****************************************************
included: openvpn for srv1

TASK [openvpn : Download sources] **********************************************
ok: [srv1]

TASK [openvpn : Check if openvpn installed already] ****************************
ok: [srv1]

TASK [openvpn : Install openvpn] ***********************************************
skipping: [srv1]

TASK [openvpn : Start openvpn] *************************************************
ok: [srv1]

TASK [openvpn : Copy ovpn file to playbook directory] **************************
ok: [srv1]

PLAY RECAP *********************************************************************
srv1                       : ok=5    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running default > idempotence

PLAY [Converge] ****************************************************************

TASK [Include openvpn role] ****************************************************
included: openvpn for srv1

TASK [openvpn : Download sources] **********************************************
ok: [srv1]

TASK [openvpn : Check if openvpn installed already] ****************************
ok: [srv1]

TASK [openvpn : Install openvpn] ***********************************************
skipping: [srv1]

TASK [openvpn : Start openvpn] *************************************************
ok: [srv1]

TASK [openvpn : Copy ovpn file to playbook directory] **************************
ok: [srv1]

PLAY RECAP *********************************************************************
srv1                       : ok=5    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Idempotence completed successfully.
INFO     Running default > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running default > verify
INFO     Running Ansible Verifier
WARNING  Skipping, verify action has no playbook.
INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Populate instance config] ************************************************
ok: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
```
