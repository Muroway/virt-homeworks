# Домашнее задание к занятию "7.1. Инфраструктура как код"

## Задача 1. Выбор инструментов. 
 
### Легенда
 
Через час совещание на котором менеджер расскажет о новом проекте. Начать работу над которым надо 
будет уже сегодня. 
На данный момент известно, что это будет сервис, который ваша компания будет предоставлять внешним заказчикам.
Первое время, скорее всего, будет один внешний клиент, со временем внешних клиентов станет больше.

Так же по разговорам в компании есть вероятность, что техническое задание еще не четкое, что приведет к большому
количеству небольших релизов, тестирований интеграций, откатов, доработок, то есть скучно не будет.  
   
Вам, как девопс инженеру, будет необходимо принять решение об инструментах для организации инфраструктуры.
На данный момент в вашей компании уже используются следующие инструменты: 
- остатки Сloud Formation, 
- некоторые образы сделаны при помощи Packer,
- год назад начали активно использовать Terraform, 
- разработчики привыкли использовать Docker, 
- уже есть большая база Kubernetes конфигураций, 
- для автоматизации процессов используется Teamcity, 
- также есть совсем немного Ansible скриптов, 
- и ряд bash скриптов для упрощения рутинных задач.  

Для этого в рамках совещания надо будет выяснить подробности о проекте, что бы в итоге определиться с инструментами:

1. Какой тип инфраструктуры будем использовать для этого проекта: изменяемый или не изменяемый?

*Изменяемый, отсутсвие четкого тз повлечет множество изменений в запущеном проекте.*

1. Будет ли центральный сервер для управления инфраструктурой?

*Я бы использовал конфигурацию Ansible без центрального сервера.*

1. Будут ли агенты на серверах?

*Опять же используя Ansible, установка агентов не требуется.*

1. Будут ли использованы средства для управления конфигурацией или инициализации ресурсов? 

*Конечно, собираем Docker-контейнеры, пакуем образа с помощью Packer, отправляем с облако с Terraform, оркестрируем через Kubernates и управляем получившейся конфигурацией через Ansible*

В связи с тем, что проект стартует уже сегодня, в рамках совещания надо будет определиться со всеми этими вопросами.

### В результате задачи необходимо

1. Ответить на четыре вопроса представленных в разделе "Легенда". 
1. Какие инструменты из уже используемых вы хотели бы использовать для нового проекта? 

*Terraform, Packer, Ansible, Docker, Kubernetes*

1. Хотите ли рассмотреть возможность внедрения новых инструментов для этого проекта? 

*Без четкого техзадания, использование новых инструментов, при необходимости, будет решено в процессе реализации проекта.*

Если для ответа на эти вопросы недостаточно информации, то напишите какие моменты уточните на совещании.


## Задача 2. Установка терраформ. 

Официальный сайт: https://www.terraform.io/

Установите терраформ при помощи менеджера пакетов используемого в вашей операционной системе.
В виде результата этой задачи приложите вывод команды `terraform --version`.

```
ad@ads-iMac ~ % terraform --version
Terraform v1.3.7
on darwin_amd64
```

## Задача 3. Поддержка легаси кода. 

В какой-то момент вы обновили терраформ до новой версии, например с 0.12 до 0.13. 
А код одного из проектов настолько устарел, что не может работать с версией 0.13. 
В связи с этим необходимо сделать так, чтобы вы могли одновременно использовать последнюю версию терраформа установленную при помощи
штатного менеджера пакетов и устаревшую версию 0.12. 

В виде результата этой задачи приложите вывод `--version` двух версий терраформа доступных на вашем компьютере 
или виртуальной машине.



Можно настроить через симлинки на разные каталоги с Terraform:


```
vagrant@vagrant:~$ wget https://hashicorp-releases.yandexcloud.net/terraform/1.3.7/terraform_1.3.7_linux_amd64.zip
--2023-01-15 18:06:16--  https://hashicorp-releases.yandexcloud.net/terraform/1.3.7/terraform_1.3.7_linux_amd64.zip
Resolving hashicorp-releases.yandexcloud.net (hashicorp-releases.yandexcloud.net)... 84.201.185.129
Connecting to hashicorp-releases.yandexcloud.net (hashicorp-releases.yandexcloud.net)|84.201.185.129|:443... connected.

vagrant@vagrant:~$ sudo unzip ./terraform_1.3.7_linux_amd64.zip -d /usr/local/bin/terraform_1_3_7
Archive:  ./terraform_1.3.7_linux_amd64.zip
  inflating: /usr/local/bin/terraform_1_3_7/terraform

vagrant@vagrant:~$ terraform --version
Terraform v0.12.31
Your version of Terraform is out of date! The latest version
is 1.3.7. You can update by downloading from https://www.terraform.io/downloads.html

vagrant@vagrant:~$ ln -s /usr/local/bin/terraform_1_3_7/terraform /usr/bin/terraform137
ln: failed to create symbolic link '/usr/bin/terraform137': Permission denied

vagrant@vagrant:~$ sudo ln -s /usr/local/bin/terraform_1_3_7/terraform /usr/bin/terraform137

vagrant@vagrant:~$ sudo chmod ugo+x /usr/bin/terraform137

vagrant@vagrant:~$ terraform137 --version
Terraform v1.3.7
on linux_amd64

vagrant@vagrant:~$ terraform --version
Terraform v0.12.31

Your version of Terraform is out of date! The latest version
is 1.3.7. You can update by downloading from https://www.terraform.io/downloads.html
```

Или использовать tfenv для переключения между версиями Terraform

```
ad@ads-iMac ~ % tfenv use 1.3.7
No installed versions of terraform matched '1.3.7:^1.3.7$'. Trying to install a matching version since TFENV_AUTO_INSTALL=true
Installing Terraform v1.3.7
Downloading release tarball from https://releases.hashicorp.com/terraform/1.3.7/terraform_1.3.7_darwin_amd64.zip
#################################################################################################### 100.0%
Downloading SHA hash file from https://releases.hashicorp.com/terraform/1.3.7/terraform_1.3.7_SHA256SUMS
Not instructed to use Local PGP (/usr/local/Cellar/tfenv/3.0.0/use-{gpgv,gnupg}) & No keybase install found, skipping OpenPGP signature verification
Archive:  /var/folders/qs/wwbh05q50m1drxh0nd5rm5200000gn/T/tfenv_download.XXXXXX.31A2OrxR/terraform_1.3.7_darwin_amd64.zip
  inflating: /usr/local/Cellar/tfenv/3.0.0/versions/1.3.7/terraform
Installation of terraform v1.3.7 successful. To make this your default version, run 'tfenv use 1.3.7'
Switching default version to v1.3.7
Default version (when not overridden by .terraform-version or TFENV_TERRAFORM_VERSION) is now: 1.3.7

ad@ads-iMac ~ % terraform --version
Terraform v1.3.7
on darwin_amd64

ad@ads-iMac ~ % tfenv use 0.12.31
Switching default version to v0.12.31
Default version (when not overridden by .terraform-version or TFENV_TERRAFORM_VERSION) is now: 0.12.31

ad@ads-iMac ~ % terraform --version
Terraform v0.12.31

Your version of Terraform is out of date! The latest version
is 1.3.7. You can update by downloading from https://www.terraform.io/downloads.html
```

---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
