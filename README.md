ln -sf /usr/share/zoneinfo/Europe/Moscow /etc/localtime

hostnamectl set-hostname freeipa.hw-otus.loc

yum install -y vim ipa-server ipa-server-dns


systemctl start firewalld
systemctl enable firewalld
firewall-cmd --permanent --add-port=53/{tcp,udp} --add-port=80/tcp --add-port=88/{tcp,udp} --add-port=123/udp --add-port=389/tcp --add-port=443/tcp --add-port=464/{tcp,udp} --add-port=636/tcp
firewall-cmd --reload

yum update nss

ipa-server-install

Установка падала с ошибкой
```
[error] CalledProcessError: Command '/usr/bin/certutil -d dbm:/etc/dirsrv/slapd-HW-OTUS-LOCAL/ -O --simple-self-signed -n HW-OTUS.LOCAL IPA CA -f /etc/dirsrv/slapd-HW-OTUS-LOCAL/pwdfile.txt' returned non-zero exit status 1
ipapython.admintool: ERROR    Command '/usr/bin/certutil -d dbm:/etc/dirsrv/slapd-HW-OTUS-LOCAL/ -O --simple-self-signed -n HW-OTUS.LOCAL IPA CA -f /etc/dirsrv/slapd-HW-OTUS-LOCAL/pwdfile.txt' returned non-zero exit status 1
ipapython.admintool: ERROR    The ipa-server-install command failed. See /var/log/ipaserver-install.log for more information
```

Помогло:
yum update nss

ipa-server-install -U -r HW-OTUS.LOCAL -n hw-otus.local -p otusotus -a otusotus --hostname=freeipa.hw-otus.local --ip-address=192.168.11.10 --mkhomedir --setup-dns --auto-forwarders --auto-reverse

--no-reverse


для клиента

ln -sf /usr/share/zoneinfo/Europe/Moscow /etc/localtime

hostnamectl set-hostname client.hw-otus.local

echo "nameserver 192.168.11.10" >> /etc/resolv.conf

yum install -y vim freeipa-client
yum update nss

ipa-client-install --principal admin@HW-OTUS.LOCAL --password otusotus --server freeipa.hw-otus.local --domain hw-otus.local --realm HW-OTUS.LOCAL --mkhomedir --force-join -U

Создаю пару ключей
```
zrad@zrDell$ ssh-keygen -t rsa -f ./id_rsa_freeipa
```

Создание пользователя во freeipa, сразу добавляя ему публичный ключ
```
ipa user-add user1 --first=Vasiliy --last=P --password password --shell=bash --sshpubkey='ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsWqMv7ZZB2WG6fWPPX5VZBY75Oz7xptwRut55Noq/DaUR2pMQ52o36i8AeHnSns6lRDPO0H2kL4NdUIWzASjfthqgawmlKfzKMkVmpGZ3w36jr/5Ln6b+HBFeBdQpAhZKSCY4NilpWM3cmjNIN1EG7evFh/TG+c+sX5RR5JArqoNjePHVWuO4i9Rb88hgLvzO0ZYVg/KZnw9Eo0da4MBqWkY8VAb9XpLPJG/QqVgp5srOUHCWmzqzpBzJt5YZ0VbL74n98/SR0hADxv7UHVMSnGK9oQwtleREzSjPHX5JPt5Aw1HW2DhNiNAb53zpE5TcKMQfprO+K46p21n2KsXX'
```

ipa user-add user1 --first=Vasiliy --last=P --password password --shell=bash --sshpubkey='ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsWqMv7ZZB2WG6fWPPX5VZBY75Oz7xptwRut55Noq/DaUR2pMQ52o36i8AeHnSns6lRDPO0H2kL4NdUIWzASjfthqgawmlKfzKMkVmpGZ3w36jr/5Ln6b+HBFeBdQpAhZKSCY4NilpWM3cmjNIN1EG7evFh/TG+c+sX5RR5JArqoNjePHVWuO4i9Rb88hgLvzO0ZYVg/KZnw9Eo0da4MBqWkY8VAb9XpLPJG/QqVgp5srOUHCWmzqzpBzJt5YZ0VbL74n98/SR0hADxv7UHVMSnGK9oQwtleREzSjPHX5JPt5Aw1HW2DhNiNAb53zpE5TcKMQfprO+K46p21n2KsXX'

На локальном хосте (или откуда планируется получать доступ к хостам под контролем freeipa) из рабочего каталога, где лежит файл id_rsa_freeipa (либо указав абсолютный путь к нему):
```
ssh -i ./id_rsa_freeipa user1@192.168.11.15
```

