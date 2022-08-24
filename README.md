# 08.01 Введение в Ansible

## Основная часть

1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте какое значение имеет факт `some_fact` для указанного хоста при выполнении playbook'a.

По умолчанию значение some_fact = 12:

   ```bash
   $ ansible-playbook site.yml -i inventory/test.yml
   
   PLAY [Print os facts] ********************************************************************************************************************************************************************************************
   
   TASK [Gathering Facts] *******************************************************************************************************************************************************************************************
   ok: [localhost]
   
   TASK [Print OS] **************************************************************************************************************************************************************************************************
   ok: [localhost] => {
       "msg": "Debian"
   }
   
   TASK [Print fact] ************************************************************************************************************************************************************************************************
   ok: [localhost] => {
       "msg": 12
   }
   
   PLAY RECAP *******************************************************************************************************************************************************************************************************
   localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
   
   ```
   
2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.
   ```bash
   $ cat group_vars/all/examp.yml
   ---
     some_fact: "all default fact"
   ```
3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.
   ```bash
   $ docker ps
   CONTAINER ID   IMAGE          COMMAND                CREATED              STATUS              PORTS     NAMES
   161d852c25a3   ubuntu:20.04   "tail -F /var/log/*"   About a minute ago   Up About a minute             ubuntu
   0df80bd3eacf   centos:7       "tail -F /var/log/*"   About a minute ago   Up About a minute             centos7
   ```
4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.

Значения some_fact - el и deb:

   ```bash
   $ ansible-playbook site.yml -i inventory/prod.yml
   
   PLAY [Print os facts] ******************************************************************************************************
   
   TASK [Gathering Facts] *****************************************************************************************************
   ok: [ubuntu]
   ok: [centos7]
   
   TASK [Print OS] ************************************************************************************************************
   ok: [centos7] => {
       "msg": "CentOS"
   }
   ok: [ubuntu] => {
       "msg": "Ubuntu"
   }
   
   TASK [Print fact] **********************************************************************************************************
   ok: [centos7] => {
       "msg": "el"
   }
   ok: [ubuntu] => {
       "msg": "deb"
   }
   
   PLAY RECAP *****************************************************************************************************************
   centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
   ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
   ```
5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились следующие значения: для `deb` - 'deb default fact', для `el` - 'el default fact'.
   ```bash
   $ cat group_vars/{deb,el}/*
   ---
     some_fact: "deb default fact"
   ---
     some_fact: "el default fact"
   ```
6. Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.
   ```bash
   $ ansible-playbook site.yml -i inventory/prod.yml
   
   PLAY [Print os facts] ******************************************************************************************************
   
   TASK [Gathering Facts] *****************************************************************************************************
   ok: [ubuntu]
   ok: [centos7]
   
   TASK [Print OS] ************************************************************************************************************
   ok: [centos7] => {
       "msg": "CentOS"
   }
   ok: [ubuntu] => {
       "msg": "Ubuntu"
   }
   
   TASK [Print fact] **********************************************************************************************************
   ok: [centos7] => {
       "msg": "el default fact"
   }
   ok: [ubuntu] => {
       "msg": "deb default fact"
   }
   
   PLAY RECAP *****************************************************************************************************************
   centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
   ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
   ```
7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.
   ```bash
   $ cat group_vars/{deb,el}/*
   $ANSIBLE_VAULT;1.1;AES256
   35396161313162366362323063396532336563643230333230626532306138626564633330323566
   6434316361363938646235666235333238363136623435630a316664643933393934386162646333
   61323366393139396638316537393938323030383966633764336139306233376265383935303033
   6464303837326436300a343537386636303661303232616166316337636366353438656363386439
   64363238373435316266626662306138336132643162376232326238326131303631376665663830
   3762623862363536326331386461356432623637333562653261
   $ANSIBLE_VAULT;1.1;AES256
   30613538346362333266326563626664353761393163616165343232316436306232313233666330
   3137316233383130353337333932346631663562333764610a343561653636613162653763656436
   32363865393430356265316638616164363332386631366438633139646636626566333365613439
   3262373832303935360a323765366663323931643733646334613064343634663339383832653061
   64393335393465343164633431323631346533353633636230643431663835653065653763663864
   3131636466363564353263653933393964363033353465613336
   ```
8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.
   ```bash
   $ ansible-playbook site.yml -i inventory/prod.yml --vault-password-file vault_pwd.txt
   
   PLAY [Print os facts] ******************************************************************************************************
   
   TASK [Gathering Facts] *****************************************************************************************************
   ok: [ubuntu]
   ok: [centos7]
   
   TASK [Print OS] ************************************************************************************************************
   ok: [centos7] => {
       "msg": "CentOS"
   }
   ok: [ubuntu] => {
       "msg": "Ubuntu"
   }
   
   TASK [Print fact] **********************************************************************************************************
   ok: [centos7] => {
       "msg": "el default fact"
   }
   ok: [ubuntu] => {
       "msg": "deb default fact"
   }
   
   PLAY RECAP *****************************************************************************************************************
   centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
   ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
   ```
9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.

```commandline
➜  ansible-learning git:(main) ✗ ansible-doc -t connection -l
ansible.netcommon.httpapi      Use httpapi to run command on network appliances
ansible.netcommon.libssh       (Tech preview) Run tasks using libssh for ssh connec...
ansible.netcommon.napalm       Provides persistent connection using NAPALM
ansible.netcommon.netconf      Provides a persistent connection using the netconf p...
ansible.netcommon.network_cli  Use network_cli to run command on network appliances
ansible.netcommon.persistent   Use a persistent unix socket for connection
community.aws.aws_ssm          execute via AWS Systems Manager
community.docker.docker        Run tasks in docker containers
community.docker.docker_api    Run tasks in docker containers
community.docker.nsenter       execute on host running controller container
community.general.chroot       Interact with local chroot
community.general.funcd        Use funcd to connect to target
community.general.iocage       Run tasks in iocage jails
community.general.jail         Run tasks in jails
community.general.lxc          Run tasks in lxc containers via lxc python library
community.general.lxd          Run tasks in lxc containers via lxc CLI
community.general.qubes        Interact with an existing QubesOS AppVM
community.general.saltstack    Allow ansible to piggyback on salt minions
community.general.zone         Run tasks in a zone instance
community.general.zone         Run tasks in a zone instance
community.libvirt.libvirt_lxc  Run tasks in lxc containers via libvirt
community.libvirt.libvirt_qemu Run tasks on libvirt/qemu virtual machines
community.okd.oc               Execute tasks in pods running on OpenShift
community.vmware.vmware_tools  Execute tasks inside a VM via VMware Tools
containers.podman.buildah      Interact with an existing buildah container
containers.podman.podman       Interact with an existing podman container
kubernetes.core.kubectl        Execute tasks in pods running on Kubernetes
local                          execute on controller
paramiko_ssh                   Run tasks via python ssh (paramiko)
psrp                           Run tasks over Microsoft PowerShell Remoting Protoco...
ssh                            connect via SSH client binary
winrm                          Run tasks over Microsoft's WinRM
(END)
```
Для подключения к *localhost* используется **local**.

10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.
    ```yaml
    $ cat inventory/prod.yml
    ---
      el:
        hosts:
          centos7:
            ansible_connection: docker
      deb:
        hosts:
          ubuntu:
            ansible_connection: docker
      local:
        hosts:
          localhost:
            ansible_connection: local
    ```
11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь что факты `some_fact` для каждого из хостов определены из верных `group_vars`.
    ```bash
    $ ansible-playbook site.yml -i inventory/prod.yml --vault-password-file vault_pwd.txt
    
    PLAY [Print os facts] **********************************************************************************************
    
    TASK [Gathering Facts] *********************************************************************************************
    ok: [localhost]
    ok: [ubuntu]
    ok: [centos7]
    
    TASK [Print OS] ****************************************************************************************************
    ok: [localhost] => {
        "msg": "Debian"
    }
    ok: [ubuntu] => {
        "msg": "Ubuntu"
    }
    ok: [centos7] => {
        "msg": "CentOS"
    }
    
    TASK [Print fact] **************************************************************************************************
    ok: [localhost] => {
        "msg": "all default fact"
    }
    ok: [centos7] => {
        "msg": "el default fact"
    }
    ok: [ubuntu] => {
        "msg": "deb default fact"
    }
    
    PLAY RECAP *********************************************************************************************************
    centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```
12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.

https://github.com/vadimburyakov/08-ansible-01/tree/main/playbook
