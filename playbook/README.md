# Самоконтроль выполненения задания

### 1. Где расположен файл с `some_fact` из второго пункта задания?

Файл `group_vars/all/examp.yml`
```yaml
$ cat group_vars/all/examp.yml
---
  some_fact: "all default fact"
```

### 2. Какая команда нужна для запуска вашего `playbook` на окружении `test.yml`?

```bash
ansible-playbook site.yml -i inventory/test.yml
```

### 3. Какой командой можно зашифровать файл?

`ansible-vault encrypt <file>`, например:

```bash
ansible-vault encrypt group_vars/deb/examp.yml
```

### 4. Какой командой можно расшифровать файл?

`ansible-vault dencrypt <file>`, например:

```bash
ansible-vault dencrypt group_vars/deb/examp.yml
```

### 5. Можно ли посмотреть содержимое зашифрованного файла без команды расшифровки файла? Если можно, то как?

`ansible-vault view <file>`, например:

```bash
ansible-vault view group_vars/deb/examp.yml
```

### 6. Как выглядит команда запуска `playbook`, если переменные зашифрованы?

Например, так:
```bash
ansible-playbook site.yml -i inventory/prod.yml --ask-vault-password
```
Или можно положить пароль в файл и тогда так:
```bash
ansible-playbook site.yml -i inventory/prod.yml --vault-password-file vault_pwd.txt
```

### 7. Как называется модуль подключения к host на windows?

- [winrm](https://docs.ansible.com/ansible/latest/user_guide/windows_winrm.html)
- [psrp](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/psrp_connection.html)

### 8. Приведите полный текст команды для поиска информации в документации ansible для модуля подключений ssh

```bash
ansible-doc -t connection ssh
```

### 9. Какой параметр из модуля подключения `ssh` необходим для того, чтобы определить пользователя, под которым необходимо совершать подключение?

`remote_user`