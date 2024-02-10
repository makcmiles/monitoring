## Задание
```
Настроить дашборд с 4-мя графиками

    память;
    процессор;
    диск;
    сеть.
    Настроить на одной из систем:
    zabbix (использовать screen (комплексный экран);
   

    Приложен скришот с требуемым графиком
```

### В качестве БД буду использовать MariaDB рекомендованной версии 11.1, установка согласно инструкции https://mariadb.org/download/?t=repo-config&d=22.04+%22jammy%22&v=11.2&r_m=docker_ru
```
root@user-VirtualBox:~# sudo apt-get install apt-transport-https curl
sudo mkdir -p /etc/apt/keyrings
sudo curl -o /etc/apt/keyrings/mariadb-keyring.pgp 'https://mariadb.org/mariadb_release_signing_key.pgp'
Чтение списков пакетов… Готово
Построение дерева зависимостей… Готово
Чтение информации о состоянии… Готово         
Уже установлен пакет curl самой новой версии (7.81.0-1ubuntu1.15).
Уже установлен пакет apt-transport-https самой новой версии (2.4.11).
Следующие пакеты устанавливались автоматически и больше не требуются:
  libgsoap-2.8.117 liblzf1 virtualbox-dkms
Для их удаления используйте «sudo apt autoremove».
Обновлено 0 пакетов, установлено 0 новых пакетов, для удаления отмечено 0 пакетов, и 204 пакетов не обновлено.
Установлено или удалено не до конца 1 пакетов.
После данной операции объём занятого дискового пространства возрастёт на 0 B.
Хотите продолжить? [Д/н] Д
Настраивается пакет virtualbox-dkms (6.1.38-dfsg-3~ubuntu1.22.04.1) …
Removing old virtualbox-6.1.38 DKMS files...
Deleting module virtualbox-6.1.38 completely from the DKMS tree.
Loading new virtualbox-6.1.38 DKMS files...
Building for 6.5.0-14-generic
Building initial module for 6.5.0-14-generic
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  4797  100  4797    0     0  19324      0 --:--:-- --:--:-- --:--:-- 19342

root@user-VirtualBox:~# nano /etc/apt/sources.list.d/matiadb.list
root@user-VirtualBox:~# cat /etc/apt/sources.list.d/matiadb.list
deb [arch=amd64 signed-by=/etc/apt/keyrings/mariadb-keyring.pgp] https://mirror.docker.ru/mariadb/repo/11.1/ubuntu jammy main
root@user-VirtualBox:~# sudo apt-get install mariadb-server
....
root@user-VirtualBox:~# systemctl enable mariadb
Synchronizing state of mariadb.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable mariadb

root@user-VirtualBox:~# systemctl status mariadb
● mariadb.service - MariaDB 11.1.4 database server
     Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/mariadb.service.d
             └─migrated-from-my.cnf-settings.conf
     Active: active (running) since Sat 2024-02-10 18:24:05 MSK; 46min ago
       Docs: man:mariadbd(8)
             https://mariadb.com/kb/en/library/systemd/
   Main PID: 20092 (mariadbd)
     Status: "Taking your SQL requests now..."
      Tasks: 8 (limit: 93556)
     Memory: 78.8M
        CPU: 939ms
     CGroup: /system.slice/mariadb.service
             └─20092 /usr/sbin/mariadbd

```
### Задаю пароль рута СУБД
```
root@user-VirtualBox:~# mysqladmin -u root password
mysqladmin: Deprecated program name. It will be removed in a future release, use '/usr/bin/mariadb-admin' instead
New password: 
Confirm new password: 
```
### Установка сервера производится по инструкции с основного сайта https://www.zabbix.com/download?zabbix=6.4&os_distribution=ubuntu&os_version=22.04&components=server_frontend_agent&db=mysql&ws=nginx
```
root@user-VirtualBox:~# wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb
--2024-02-10 16:31:04--  https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb
Распознаётся repo.zabbix.com (repo.zabbix.com)… 178.128.6.101, 2604:a880:2:d0::2062:d001
Подключение к repo.zabbix.com (repo.zabbix.com)|178.128.6.101|:443... соединение установлено.
HTTP-запрос отправлен. Ожидание ответа… 200 OK
Длина: 3744 (3,7K) [application/octet-stream]
Сохранение в: ‘zabbix-release_6.4-1+ubuntu22.04_all.deb’

zabbix-release_6.4-1+ubuntu22. 100%[====================================================>]   3,66K  --.-KB/s    за 0s      

2024-02-10 16:31:04 (590 MB/s) - ‘zabbix-release_6.4-1+ubuntu22.04_all.deb’ сохранён [3744/3744]

root@user-VirtualBox:~# dpkg -i zabbix-release_6.4-1+ubuntu22.04_all.deb
Выбор ранее не выбранного пакета zabbix-release.
(Чтение базы данных … на данный момент установлено 294430 файлов и каталогов.)
Подготовка к распаковке zabbix-release_6.4-1+ubuntu22.04_all.deb …
Распаковывается zabbix-release (1:6.4-1+ubuntu22.04) …
Настраивается пакет zabbix-release (1:6.4-1+ubuntu22.04) …
root@user-VirtualBox:~# apt update
Сущ:1 http://ru.archive.ubuntu.com/ubuntu jammy InRelease
Сущ:2 http://ru.archive.ubuntu.com/ubuntu jammy-updates InRelease                                                          
Сущ:3 http://ru.archive.ubuntu.com/ubuntu jammy-backports InRelease                                                        
Сущ:4 https://mirror.docker.ru/mariadb/repo/11.2/ubuntu jammy InRelease                                                    
Сущ:5 https://packages.microsoft.com/ubuntu/22.04/prod jammy InRelease                                                     
Сущ:6 http://security.ubuntu.com/ubuntu jammy-security InRelease                                                           
Сущ:7 https://download.docker.com/linux/ubuntu jammy InRelease                                                             
Сущ:8 http://download.virtualbox.org/virtualbox/debian jammy InRelease                                                     
Пол:9 https://repo.zabbix.com/zabbix/6.4/ubuntu jammy InRelease [4 958 B]                                                  
Пол:10 https://repo.zabbix.com/zabbix/6.4/ubuntu jammy/main Sources [1 940 B]
Пол:11 https://repo.zabbix.com/zabbix/6.4/ubuntu jammy/main amd64 Packages [5 484 B]
Получено 12,4 kB за 2с (7 933 B/s)         
Чтение списков пакетов… Готово
Построение дерева зависимостей… Готово
Чтение информации о состоянии… Готово         
Может быть обновлено 204 пакета. Запустите «apt list --upgradable» для их показа.
....

root@user-VirtualBox:~# apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
....
```
### Настройка СУБД: создание БД, пользователя zabbix@localhost с паролем password, предоставление прав. Импорт исходной схемы Забикса
```
root@user-VirtualBox:~# mysql -uroot -p
mysql: Deprecated program name. It will be removed in a future release, use '/usr/bin/mariadb' instead
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 32
Server version: 11.2.3-MariaDB-1:11.2.3+maria~ubu2204 mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> create database zabbix character set utf8mb4 collate utf8mb4_bin;
Query OK, 1 row affected (0,000 sec)

MariaDB [(none)]> create user zabbix@localhost identified by 'password';
Query OK, 0 rows affected (0,001 sec)

MariaDB [(none)]> grant all privileges on zabbix.* to zabbix@localhost;
Query OK, 0 rows affected (0,001 sec)

MariaDB [(none)]> set global log_bin_trust_function_creators = 1;
Query OK, 0 rows affected (0,000 sec)

MariaDB [(none)]> quit; 
Bye

root@user-VirtualBox:~# zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
mysql: Deprecated program name. It will be removed in a future release, use '/usr/bin/mariadb' instead
Enter password: 

root@user-VirtualBox:~# mysql -uroot -p
mysql: Deprecated program name. It will be removed in a future release, use '/usr/bin/mariadb' instead
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 39
Server version: 11.2.3-MariaDB-1:11.2.3+maria~ubu2204 mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> set global log_bin_trust_function_creators = 0;
Query OK, 0 rows affected (0,000 sec)

MariaDB [(none)]> quit
Bye
```
### Правка конфига /etc/zabbix/zabbix_server.conf, прописал пароль DBPassword=password
```
root@user-VirtualBox:~# cat /etc/zabbix/zabbix_server.conf | grep DBPassword=
# DBPassword=
DBPassword=password
```
### Конфигурирование /etc/zabbix/nginx.conf
```
root@user-VirtualBox:~# cat /etc/zabbix/nginx.conf | grep listen
        listen          8080;
```
### Запуск процессов сервера и агента
```
root@user-VirtualBox:~# systemctl restart zabbix-server zabbix-agent nginx php8.1-fpm
Job for zabbix-server.service failed because the service did not take the steps required by its unit configuration.
See "systemctl status zabbix-server.service" and "journalctl -xeu zabbix-server.service" for details.
root@user-VirtualBox:~# systemctl enable zabbix-server zabbix-agent nginx php8.1-fpm 
Synchronizing state of zabbix-server.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable zabbix-server
Synchronizing state of zabbix-agent.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable zabbix-agent
Synchronizing state of nginx.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable nginx
Synchronizing state of php8.1-fpm.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable php8.1-fpm
Created symlink /etc/systemd/system/multi-user.target.wants/zabbix-server.service → /lib/systemd/system/zabbix-server.service.
```
### Теперь можно через веб-браузер проверить конфигурирацию и настроить мониторинг нужных параметров. Как правило, предварительно требуется "раскидать" агентов по узлам, с которых будут собираться параметры.
### Скрин с графиком прилагается.
