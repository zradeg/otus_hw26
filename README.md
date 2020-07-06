# LDAP. Централизованная авторизация и аутентификация

## Задачи
1. Установить FreeIPA;
2. Написать Ansible playbook для конфигурации клиента;
3. \* Настроить аутентификацию по SSH-ключам;
4. \*\* Firewall должен быть включен на сервере и на клиенте.


## Выполнение

Стенд с сервером и клиентом FreeIPA разворачивается автоматически посредством команды vagrant up

1. Установка FreeIPA

Установка падала с ошибкой
```
[error] CalledProcessError: Command '/usr/bin/certutil -d dbm:/etc/dirsrv/slapd-HW-OTUS-LOCAL/ -O --simple-self-signed -n HW-OTUS.LOCAL IPA CA -f /etc/dirsrv/slapd-HW-OTUS-LOCAL/pwdfile.txt' returned non-zero exit status 1
ipapython.admintool: ERROR    Command '/usr/bin/certutil -d dbm:/etc/dirsrv/slapd-HW-OTUS-LOCAL/ -O --simple-self-signed -n HW-OTUS.LOCAL IPA CA -f /etc/dirsrv/slapd-HW-OTUS-LOCAL/pwdfile.txt' returned non-zero exit status 1
ipapython.admintool: ERROR    The ipa-server-install command failed. See /var/log/ipaserver-install.log for more information
```

Помогло:

```
yum update nss
```

Аналогично и для клиента.



2. Ansible playbook

И сервер и клиент успешно разворачиваются через playbook.



3. Настройка авторизации по ssh-ключам

Создаю пару ключей, которые будут использоваться для авторизации пользователя user1

```
zrad@zrDell$ ssh-keygen -t rsa -f ./id_rsa_freeipa
```

Создаю пользователя во FreeIPA, сразу добавляю публичный ключ
```
ipa user-add user1 --first=Vasiliy --last=P \
--password password --shell=bash \
--sshpubkey='ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsWqMv7ZZB2WG6fWPPX5VZBY75Oz7xptwRut55Noq/DaUR2pMQ52o36i8AeHnSns6lRDPO0H2kL4NdUIWzASjfthqgawmlKfzKMkVmpGZ3w36jr/5Ln6b+HBFeBdQpAhZKSCY4NilpWM3cmjNIN1EG7evFh/TG+c+sX5RR5JArqoNjePHVWuO4i9Rb88hgLvzO0ZYVg/KZnw9Eo0da4MBqWkY8VAb9XpLPJG/QqVgp5srOUHCWmzqzpBzJt5YZ0VbL74n98/SR0hADxv7UHVMSnGK9oQwtleREzSjPHX5JPt5Aw1HW2DhNiNAb53zpE5TcKMQfprO+K46p21n2KsXX'
```



## Проверка

На локальном хосте (или откуда планируется получать доступ к хостам под контролем freeipa) из рабочего каталога, где лежит файл id_rsa_freeipa (либо указав абсолютный путь к нему):

Логинимся на клиент:

```
ssh -i ./id_rsa_freeipa user1@192.168.11.15
```

Логинимся на сервер:

```
ssh -i ./id_rsa_freeipa user1@192.168.11.10
```

