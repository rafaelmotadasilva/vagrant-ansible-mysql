<h1>
    <a href="https://www.dio.me/">
     <img align="center" width="40px" src="https://www.ansible.com/images/project-logos/ansible-core.svg"></a>
    <span> Criando uma role do MySQL no Ansible</span>
</h1>

Repositório desenvolvido para fins educativos.

## Objetivo

Criar uma máquina virtual através de um arquivo do Vagrantfile. Configurar o provisionamento com Ansible e criar uma role do MySQL para realizar as seguintes tarefas:

- Instalar pacotes necessários.
- Instalar o serviço do MySQL.
- Criar uma variável para definir a senha do root.

## Vagrantfile

Este é um exemplo simples de `Vagrantfile`, que cria uma máquina virtual e configura o provisionamento com Ansible.

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.network "public_network"
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end
end
```

Se mais de uma interface de rede estiver disponível na máquina host, o Vagrant solicitará que você escolha qual interface a máquina virtual deve fazer a ponte.

## Executando o script

Para criar e configurar a máquina virtual, execute o seguinte comando no terminal:

```
vagrant up
```

Isso criará e iniciará a máquina virtual e aplicará a configuração do Ansible.

```
.
├── playbook.yml
├── README.md
├── roles
│   └── mysql
│       ├── tasks
│       │   └── main.yml
│       └── vars
│           └── main.yml
└── Vagrantfile
```

## playbook.yml

Um exemplo básico de um playbook Ansible para usar a role `mysql`:

```
---
- hosts: all
  become: yes
  roles:
    - mysql
```

## roles/mysql/tasks/main.yml

Crie a role `mysql` para realizar as seguintes tarefas:

```
---
- name: Atualizar o cache do apt
  apt:
    update_cache: yes
  become: yes

- name: Instalar pacotes necessários
  apt:
    name:
      - python3-pip
      - python3-pymysql
    state: present
  become: yes

- name: Instalar mysqlclient e configparser
  pip:
    name: 
      - configparser
  become: yes

- name: Instalar MySQL Server
  apt:
    name: mysql-server
    state: present
  become: yes

- name: Definir senha do root do MySQL
  mysql_user:
    name: root
    host: localhost
    password: "{{ mysql_root_password }}"
    login_unix_socket: /var/run/mysqld/mysqld.sock
    state: present
  become: yes

- name: Garantir que o serviço MySQL está em execução
  service:
    name: mysql
    state: started
    enabled: yes
  become: yes
  ```