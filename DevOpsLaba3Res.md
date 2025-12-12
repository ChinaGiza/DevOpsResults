**Лабораторная работ №3**   
по дисциплине: **Основы DevOps**   
на тему: **Основы Ansible в DevOps** 
Группа: **ПИ-431Б**   
Студент: **Махмутов Чингиз Ильдарович**   
Принял: **Аитбаев Вадим**   

___
# 1. Цели работы
Цель работы является освоение практических навыков по автоматизации задач с использованием `Ansible`.

___
# 2. Теоретическая часть
## 2.1. Что такое Ansible

`Ansible` помогает автоматизировать настройку удалённых серверов в сети и управление ими. Он позволяет управлять конфигурациями машин, доставлять и развёртывать приложения, а также выполнять другие задачи администрирования без ручного вмешательства.

Обычным способом нужно вручную прописывать каждую команду или скрипт и по кругу запускать их на серверах. Когда серверов много, это процесс становится сложным и трудоёмким. C помощью `Ansible` всю настройку можно прописать в одном конфигурационном файле, который программа разошлёт на большое количество машин. Такой метод работы называют `IaaC` — `Infrastructure As A Code` («инфраструктура как код»).
## 2.2. Для чего используют Ansible

`Ansible` используют сетевые администраторы, `DevOps`-инженеры, разработчики ПО и системные администраторы. Вот основные функции программы:
* Управление конфигурацией. `Ansible` позволяет управлять конфигурациями серверов и поддерживать их, чтобы они оставались в согласованном состоянии. С его помощью можно проводить установку и настройку программного обеспечения, управлять файлами конфигурации и обеспечивать выполнение политик безопасности.
* Развёртывание приложений. `Ansible` автоматизирует процесс развёртывания приложений, включая установку необходимых зависимостей, копирование файлов приложений, настройку серверов и запуск сервисов.
* Оркестрация. Это процесс координации и управления выполнения задач на нескольких серверах или системах для обеспечения их согласованного и эффективного взаимодействия. `Ansible` позволяет проводить оркестрацию нескольких серверов — например, при обновлении кластеров или выполнении сложных операций, требующих координации между различными сервисами и машинами.
* Управление облачной инфраструктурой. `Ansible` поддерживает множество провайдеров облачных услуг, включая `AWS`, `Azure` и `Google Cloud`. Он позволяет создавать, изменять и удалять облачные ресурсы, включая виртуальные машины, сети, хранилища и другие компоненты.
* Управление контейнерами. Контейнеры — это небольшие самодостаточные единицы, которые включают всё необходимое для выполнения приложения: код, зависимости, библиотеки и конфигурационные файлы. Они обеспечивают изоляцию приложения от системы, на которой оно работает, что позволяет легко переносить и запускать приложения в различных средах. `Ansible` интегрируется с `Docker` и `Kubernetes`, что позволяет автоматизировать развёртывание и масштабирование контейнеризированных приложений, а также управление ими.
* Обеспечение безопасности. `Ansible` может использоваться для автоматизации задач безопасности, включая установку обновлений и патчей, настройку файерволов, управление учётными записями пользователей и применение политик безопасности.
* Мониторинг и оповещение. `Ansible` помогает в настройке систем мониторинга, таких как `Prometheus`, `Nagios`, `Zabbix`, и управлении ими, а также в настройке систем оповещения. Он помогает провести установку агентов мониторинга, настройку метрик и создание уведомлений.
* Резервное копирование и восстановление. `Ansible` можно использовать для автоматизации процессов резервного копирования и восстановления данных. Это включает настройку задач для регулярного создания резервных копий и автоматизированное восстановление данных в случае сбоя.
* Управление пользователями и группами. `Ansible` упрощает управление учётными записями пользователей и группами на серверах, что важно для соблюдения политик безопасности и управления доступом.
* Создание тестовых сред и сред разработки. `Ansible` позволяет быстро и легко создавать и настраивать тестовые и сред разработки, обеспечивая их идентичность продуктивным системам. Это помогает разработчикам и тестировщикам работать в условиях, максимально приближенных к реальным.

___
# 3. Практическая часть

## 3.1. Установка `Ansible` на `Ubuntu`

Для установки `Ansible` использовались следующие команды:
* `sudo apt install -y python3 python3-pip python3-venv` - для установки `Python` и `pip`.
* `sudo apt install -y ansible` - для установки `Ansible`.
* `ansible --version` - проверка версии `Ansible`.

Ожидаемый результат:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/AnsibleVersion.png)
## 3.2. Подготовка `SSH` ключей для управляемых машин

По следующей команде генерируем `SSH` ключевые пары:
``` bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/ansible_key -N ""
```
Параметры данной команды:
* `-t rsa` - используемый алгоритм шифрования
* `-b 4096` - размер ключа в битах
* `-f ~/.ssh/ansible_key` - путь сохранения приватного ключа
* `-N ""` - пустой пароль для ключа

Установим права доступа на приватный ключ:
``` bash
chmod 600 ~/.ssh/ansible_key
chmod 644 ~/.ssh/ansible_key.pub
```
## 3.3. Запуск управляемого контейнера в `Docker`

Создадим `Dockerfile` со следующими параметрами:
``` Dockerfile
FROM ubuntu:22.04

# Установка необходимых пакетов
RUN apt-get update && apt-get install -y \
	openssh-server \            
	openssh-client \             
	python3 \                   
	python3-pip \                   
	sudo \                    
	curl \                    
	wget \                    
	git \                           
	nano \                    
	htop \                  
	&& rm -rf /var/lib/apt/lists/*
	
# Создание пользователя ansible
RUN useradd -m -s /bin/bash ansible && \
	echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
	
# Настройка SSH
# Аутентификация по публичному ключу включена
# Аутентификация по паролю отключена
RUN mkdir -p /run/sshd && \
	sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config && \
	sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
	
# Настройка SSH для пользователя ansible
RUN mkdir -p /home/ansible/.ssh && \
	chown -R ansible:ansible /home/ansible/.ssh && \
	chmod 700 /home/ansible/.ssh

# Запуск SSH-сервера в foreground режиме
CMD ["/usr/sbin/sshd", "-D"]
```

Создадим `docker-compose.yaml` со следующими параметрами:
``` docker-compose.yml
version: '3.8'

services:
	managed-host:
		container_name: ansible-managed-host
		build: .
		ports:
		  - "2222:22"
		environment:
		  - ansible-net
		restart: unless-stopped
		healthcheck:
			test: ["CMD", "service", "ssh", "status"]
			interval: 10s
			timeout: 5s
			retries: 3

networks:
	ansible-net:
		driver: bridge
```

При помощи следующих команд соберем и запустим контейнер:
``` bash
# Сборка образа
docker-compose build

# Запуск контейнера в фоновом режиме
docker-compose up -d
```

Проверка контейнера:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/DockerResult.png)

Копируем публичный SSH ключ в контейнер:
``` sh
# Создание директории .ssh
docker exec ansible-managed-host mkdir -p /home/ansible/.ssh

# Копирование публичного SSH ключа
docker cp ~/.ssh/ansible_key.pub ansible-host:/home/ansible/.ssh/aithorized_keys

# Установка прав доступа
docker exec ansible-managed-host chown -R ansible:ansible /home/ansible/.ssh
docker exec ansible-managed-host chmod 700 /home/ansible/.ssh
docker exec ansible-managed-host chmod 600 /home/ansible/.ssh/authorized_keys
```
## 3.4. Проверка `SSH` подключения к контейнеру

Проверим подключение к контейнеру при помощи следующей команды:
```
ssh -i ~/.ssh/ansible_key -p 2222 ansible@localhost
```

Результат проверки:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/ConnectionResult.png)
## 3.5. Создание инвентарного файла `Ansible`

Создадим `inventory.ini` со следующими параметрами:
``` ini
[managed_hosts]
managed1 ansible_host=localhost ansible_port=2222 ansible_user=ansible ansible_ssh_private_key=~/.ssh/ansible_key ansible_python_interpreter=/usr/bin/python3

[all:vars]
ansible_ssh_common_args=-o StrictHostKeyChecking=no
```
Параметры:
* `[managed_hosts]` - группа хостов
* `managed1` - имя хоста в инвентаре
* `ansible_host=localhost` - `IP`-адрес или `FQDN`
* `ansible_port=2222` - порт `SSH`
* `ansible_user=ansible` - пользователь для подключения
* `ansible_ssh_private_key_file` - путь к приватному `SSH` ключу
* `ansible_python_interpreter` - путь к интерпретатору `Python` на управляемом хосте
* `ansible_ssh_common_args` - отключение проверки ключа хоста

Проверим инвентарь при помощи следующей команды:
``` sh
ansible-inventory -i inventory.ini --list
```

Результат проверки:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/InventoryResult.png)
## 3.6. Проверим подключение `Ansible` к управляемому хосту

Первой проверкой является тест `ping`. Команда, при помощи которой выполняется проверка:
``` sh
ansible -i inventory.ini managed_hosts -m ping
```

Результат первой проверки:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/AnsibleTestResult1.png)

Второй проверкой является сбор информации о системе. Команда, при помощи которой выполняется проверка:
``` sh
ansible -i inventory.ini managed1 -m setup
```

Результат второй проверки:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/AnsibleTestResult2.png)

Третьей проверкой является выполнение простой команды. Команда, при помощи которой выполняется проверка:
``` sh
ansible -i inventory.ini managed1 -m command -a "uname -a"
```

Результат третьей проверки:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/AnsibleTestResult3.png)
## 3.7. Создание и запуск `Ansible Playbook`

Создадим `playbook.yml` со следующими параметрами:
``` yml
---
- name: Демонстрационный playbook для Ansible
  hosts: managed_hosts
  gather_facts: yes
  
  tasks:
    - name: Обновление списка пакетов
      apt:
        update_cache: yes
        cache_valid_time: 3600
      become: yes

    - name: Установка требуемых пакетов
      apt:
        name:
          - curl
          - wget
          - git
          - nano
        state: present
      become: yes

    - name: Создание тестовой директории
      file:
        path: /tmp/ansible_test
        state: directory
        mode: '0755'

    - name: Создание тестового файла с содержимым
      copy:
        content: |
          Hello from Ansible!
          This is a test file created by Ansible playbook.
          Hostname: {{ ansible_hostname }}
          OS: {{ ansible_distribution }} {{ ansible_distribution_version }}
          Date: {{ ansible_date_time.iso8601 }}
        dest: /tmp/ansible_test/test_file.txt
        mode: '0644'

    - name: Вывод содержимого файла
      command: cat /tmp/ansible_test/test_file.txt
      register: file_content

    - name: Отображение содержимого файла
      debug:
        msg: "{{ file_content.stdout }}"

    - name: Сбор информации о системе
      debug:
        msg: |
          System: {{ ansible_system }}
          Hostname: {{ ansible_hostname }}
          Uptime: {{ ansible_uptime_seconds }} seconds
          CPU cores: {{ ansible_processor_cores }}
          Total memory: {{ ansible_memtotal_mb }} MB
```

Запустим данный `playbook` при помощи следующей команды:
``` bash
ansible-playbook -i inventory.ini playbook.yml
```

Результат выполнения команды:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/PlaybookResult.png)

## 3.8.1. Выполнение базовых `ad-hoc` команд
Получение информации о ядрах `CPU` управляемого хоста. Команда:
``` sh
ansible -i inventory.ini managed1 -m setup -a "filter=ansible_processor_cores"
```

Результат выполнения команды:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/CommandResult1.png)

Получение информации о свободном места на диске. Команда:
``` sh
ansible -i inventory.ini managed1 -m command -a "df -h"
```

Результат выполнения команды:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/CommandResult2.png)

Получение списка всех пользователей. Команда:
``` sh
ansible -i inventory.ini managed1 -m command -a "cat /etc/passwd"
```

Результат выполнения команды:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/CommandResult3.png)

Изменение временной зоны хоста на `UTC`. Команда:
``` sh
ansible -i inventory.ini managed1 -m command -a "timedatectl set-timezone utc"
```

Результат выполнения команды:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/CommandResult4.png)
## 3.8.2. Работа с файлами

Создадим `playbook.yml` со следующими параметрами:
``` yml
---
- name: Work with files
  hosts: managed_hosts
  tasks:
    - name: Create multiple directories
      file:
        path: /tmp/{{ item }}
        state: directory
        mode: '0755'
      loop:
        - test_dir1
        - test_dir2
        - test_dir3

    - name: Create files in directories
      copy:
        content: "This is {{ item }} file\n"
        dest: /tmp/{{ item }}/content.txt
      loop:
        - test_dir1
        - test_dir2
        - test_dir3

    - name: Display files
      command: cat /tmp/{{ item }}/content.txt
      loop:
        - test_dir1
        - test_dir2
        - test_dir3
      register: file_content

    - name: Show file contents
      debug:
        msg: "{{ item.stdout }}"
      loop: "{{ file_content.results }}"
```

Запустим данный `playbook` при помощи следующей команды:
``` bash
ansible-playbook -i inventory.ini task3_files.yml
```

Результат выполнения команды:
![alt text](https://github.com/ChinaGiza/DevOpsResults/blob/main/DevOpsLaba3Files/ThreeFilesResult.png)

___
# Вывод

В данной лабораторной работе были освоены практические навыки автоматизации задач с использованием `Ansible`.
