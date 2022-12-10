
# Домашнее задание к занятию "2. Применение принципов IaaC в работе с виртуальными машинами"

## Задача 1

**Опишите своими словами основные преимущества применения на практике IaaC паттернов.**

Ускорение производства и вывода продукта на рынок.
Отсутствие проблемы зоопарка разных конфигураций и исключение человеческого фактора из настройки одинаковых конфигураций сервисов.
Увеличение эффективности и скорости разработки, за счет снижения расходов и  времени затрачиваемые на рутинные операции.

**Какой из принципов IaaC является основополагающим?**

Идемпотентность - это свойство объекта или операции, при повторном выполнении которой мы получаем результат идентичный предыдущему и всем последующим выполнения.

## Задача 2

**Чем Ansible выгодно отличается от других систем управление конфигурациями?**

Разрабатывается крупным игроком таким как RedHat, что обеспечивает массовость и качественную поддержку.
Использует популярный и простой язык программирования Python.
Использует push метод, при котором конфигурация разносится по системе с мастер-сервера.


**Какой, на ваш взгляд, метод работы систем конфигурации более надёжный push или pull?**
 
На мой взгляд push метод более надежный, потому позволяет определять и контролировать конфигурацию которая будет развернута.
Pull метод с подходом когда сервера сами запрашивают конфигурацию, считаю менее надежным, ввиду возможных ошибок конфигурации и человеческого фактора.

## Задача 3
**Установить на личный компьютер:**

- **VirtualBox**
- **Vagrant**
- **Ansible**

**Приложить вывод команд установленных версий каждой из программ, оформленный в markdown.**

```
ansible --version
ansible [core 2.13.5]
  config file = /Volumes/MacDATA/Users/ad/ansible.cfg
  configured module search path = ['/Volumes/MacDATA/Users/ad/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/Cellar/ansible/6.5.0/libexec/lib/python3.10/site-packages/ansible
  ansible collection location = /Volumes/MacDATA/Users/ad/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.10.8 (main, Oct 13 2022, 10:17:43) [Clang 14.0.0 (clang-1400.0.29.102)]
  jinja version = 3.1.2
  libyaml = True
```

```
vagrant version
Installed Version: 2.3.2
Latest Version: 2.3.2

You're running an up-to-date version of Vagrant!
```
```
vagrant@server1:~$ sudo docker version
Client: Docker Engine - Community
 Version:           20.10.21
 API version:       1.41
 Go version:        go1.18.7
 Git commit:        baeda1f
 Built:             Tue Oct 25 18:02:21 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.21
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.18.7
  Git commit:       3056208
  Built:            Tue Oct 25 18:00:04 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.9
  GitCommit:        1c90a442489720eec95342e1789ee8a5e1b9536f
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
  ```
  