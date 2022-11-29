# 3-node-swarm

Implementação de um cluster Swarm com 3 nós, utilizando Ansible + Vagrant(com VirtualBox como Hypervisor).

# Sumário

### Vagrant
| Arquivo | Resumo |
|---|---|
| [Vagrantfile](https://github.com/fermino-linux/3-node-swarm/blob/main/Vagrantfile) | Definições das VM's
| [install.sh](https://github.com/fermino-linux/bash-training/blob/main/ansible/install.sh) | Script de instalação do Ansible

### Ansible
| Role | Resumo | 
|---|---|
|[common](https://github.com/fermino-linux/3-node-swarm/tree/main/roles/common)| Instalação do Docker |
|[tokens](https://github.com/fermino-linux/3-node-swarm/tree/main/roles/tokens)| Inicializa o cluster e salva os tokens de acesso |
|[joins](https://github.com/fermino-linux/3-node-swarm/tree/main/roles/joins) | Autentica usando os tokens e insere os nós no cluster |


# Descrição
O Vagrantfile define 3 VM's e suas configurações básicas, sendo que a VM chamada de **master** é a responsável pelo implementação do cluster de fato, ela executa
um script que fiz em outro projeto para a instalação do Ansible, em seguida através do provisioner **ansible_local**, ela faz a execução da playbook principal
que executa cada uma das roles, a execução segue a seguinte ordem:

1. **common**: 
    1. Adiciona o repositório para instalação do Docker
    1. Executa a instalação
    1. Habilita a execução do serviço do Docker
    1. Concede permissões para que o usuário Vagrant possa gerenciar o Docker.
1. **tokens**: 
    1. Inicia o cluster Swarm no node master
    1. Salva os tokens de manager e worker
    1. Cópia os tokens para os nodes
1. **joins**:
    1. Faz a leitura dos tokens
    2. Utiliza os tokens para adicionar os nós

### Modificando o Cluster
Por padrão esse 3-node-swarm utiliza a configuração 1 Manager e 2 Workers, mas é possível altera-la para utilizar 2 Managers ou até 3 se necessário, basta modificar este [arquivo](https://github.com/fermino-linux/3-node-swarm/blob/main/roles/joins/tasks/main.yaml), alterando o valor da propriedade ***join_token*** do nó selecionado para ***"{{ manager }}"***.
```
- name: Adiciona o primeiro nó ao cluster 
  community.docker.docker_swarm:
    state: join
    join_token: "{{ worker }}"
    advertise_addr: "{{ ansible_host }}"
    remote_addrs: ["{{ hostvars['m1'].ansible_host }}:2377"]
  when: ansible_hostname == "node-1"

- name: Adiciona o segundo nó ao cluster 
  community.docker.docker_swarm:
    state: join
    join_token: "{{ worker }}"
    advertise_addr: "{{ ansible_host }}"
    remote_addrs: ["{{ hostvars['m1'].ansible_host }}:2377"]
  when: ansible_hostname == "node-2"
``` 

### Atenção
Este projeto foi feito para fins de estudo, caso você decida implementar algo parecido em um ambiente
de produção utilize o ansible-vault(ou algo parecido) para esconder os dados sensíveis
do projeto(como credenciais de usuário, por exemplo).
Você pode ler mais sobre o ansible-vault [aqui](https://docs.ansible.com/ansible/latest/vault_guide/index.html) e caso você decida utiliza-lo em conjunto com o Vagrant terá que adicionar
algumas opções no seu Vagrantfile, você pode ler mais sobre opções relacionadas ao Ansible em conjunto com o Vagrant [aqui](https://developer.hashicorp.com/vagrant/docs/provisioning/ansible_common)

# Execução
```
vagrant up
``` 





