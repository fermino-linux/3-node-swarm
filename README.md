# 3-node-swarm

Implementação de um cluster Swarm com 3 nós, utilizando Ansible + Vagrant(com VirtualBox como Hypervisor).

# Sumário

### Vagrant
| Arquivo | Descrição |
|---|---|
| [Vagrantfile](https://github.com/fermino-linux/3-node-swarm/blob/main/Vagrantfile) | Definições das VM's
| [install.sh](https://github.com/fermino-linux/bash-training/blob/main/ansible/install.sh) | Script de instalação do Ansible

### Ansible
| Artefato | Descrição | 
|---|---|
|[common](https://github.com/fermino-linux/3-node-swarm/tree/main/roles/common)| Instalação do Docker |
|[tokens](https://github.com/fermino-linux/3-node-swarm/tree/main/roles/tokens)| Inicializa o cluster e salva os tokens de acesso |
|[joins](https://github.com/fermino-linux/3-node-swarm/tree/main/roles/joins) | Autentica usando os tokens e insere os nós no cluster |


# Descrição
O Vagrantfile define 3 VM's e suas configurações básicas, sendo que a VM chamada de **master** é a responsável pelo implementação do cluster de fato, ela executa
um script que fiz em outro projeto para a instalação do Ansible, em seguida através do provisioner **ansible_local**, ela faz a execução da playbook principal
que executa cada uma das roles.

A primeira role a ser executada é a **common**, que adiciona o repositório do Docker, instala o mesmo, habilita seu serviço e concede permissões para que o usuário Vagrant
possa gerencia-lo.

Em seguida passamos para a execução da role **tokens**, esta inicia o Docker Swarm na VM **master**, armazena os tokens de acesso gerados por ele em um arquivo e
faz cópia destes arquivos para cada um dos nodes que serão adicionados ao cluster.

Por último ocorre a execução da role **joins**, ela faz a leitura dos tokens presentes nos nodes e os utiliza para popular o cluster, por padrão os dois
nodes são adicionados como workers mas é possível adicionar um deles como manager ou ambos se necessário, pois a role anterior salva os dois tipos de tokens
em cada uma das máquinas.


### Atenção
Este projeto foi feito para fins de estudo, caso você decida implementar algo parecido em um ambiente
de produção utilize o ansible-vault(ou algo parecido) para esconder dados sensíveis.
do projeto.
Você pode ler mais sobre o ansible-vault [aqui](https://docs.ansible.com/ansible/latest/vault_guide/index.html) e caso você decida utiliza-lo em conjunto com o Vagrant terá que adicionar
algumas opções no seu Vagrantfile, você pode ler mais sobre opções relacionadas ao Ansible em conjunto com o Vagrant [aqui](https://developer.hashicorp.com/vagrant/docs/provisioning/ansible_common)

# Execução
```
vagrant up
``` 





