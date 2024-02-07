# lab-ansible-zabbix-grafana-prometheus
Laboratório para ensaios com do Ansible, Zabbix, Grafana e Prometheus

# Pré requisitos:

Uma máquina com os seguintes softwares instalados:

Virtualbox
Vagrant

## Criando o script do Vagrantfile:

Provisionamento das máquinas com o Vagrant

[Vagrantfile](https://github.com/andersonvm/lab-ansible-zabbix-grafana-prometheus/blob/main/Vagrantfile)

Este arquivo Vagrantfile cria três máquinas virtuais usando diferentes boxes para CentOS 7, Debian Stretch 64-bit e Ubuntu Bionic 64-bit. Cada máquina terá seu próprio endereço IP na rede interna 192.168.50.X. Certifique-se de ter as boxes CentOS 7, Debian Stretch 64-bit e Ubuntu Bionic 64-bit disponíveis no Vagrant para que este script funcione corretamente.

Para criar as máquinas, basta colocar este script em um arquivo chamado Vagrantfile e executar vagrant up no diretório onde o arquivo está localizado. Isso iniciará e provisionará as três máquinas virtuais definidas no script.

## Geração das chaves SSH

No terminal, use o comando vagrant ssh seguido do nome da máquina. Por exemplo, para acessar a máquina CentOS, digite:

```
vagrant ssh centos
```

Isso abrirá um terminal conectado à máquina CentOS via SSH.

## Gere um par de chaves SSH

Uma vez dentro da máquina, execute o comando ssh-keygen. Ele criará um par de chaves SSH (pública e privada) por padrão no diretório ~/.ssh/. Pressione Enter para aceitar as configurações padrão (normalmente você pode deixar a senha em branco).

## Copie a chave pública para a outra máquina

Após gerar as chaves, você precisa copiar a chave pública para a outra máquina para permitir o acesso.
Para visualizar a chave pública gerada, use o comando:

```
cat ~/.ssh/id_rsa.pub
```

Isso exibirá a chave pública.
Copie a chave pública exibida.

## Adicione a chave pública à outra máquina

Acesse a outra máquina usando vagrant ssh como no Passo 1 e navegue até o diretório ~/.ssh/ (crie-o se não existir):

```
mkdir -p ~/.ssh && chmod 700 ~/.ssh
```

Abra o arquivo ~/.ssh/authorized_keys (ou crie-o se não existir) usando um editor de texto como o nano ou vim:

```
vim ~/.ssh/authorized_keys
```

Cole a chave pública que você copiou da primeira máquina para este arquivo e salve as alterações.

## Teste a conexão

Tente acessar a outra máquina a partir da primeira usando ssh:

```
ssh VAGRANT_USER@IP_DA_OUTRA_MAQUINA
```

Substitua VAGRANT_USER pelo usuário da máquina destino e IP_DA_OUTRA_MAQUINA pelo endereço IP dela.

Se tudo estiver configurado corretamente, você deve conseguir acessar a outra máquina sem digitar uma senha. Certifique-se de que as permissões dos diretórios e arquivos ~/.ssh/ estejam configuradas corretamente (normalmente, permissões 700 para 
~/.ssh/ e 600 para ~/.ssh/authorized_keys).


### Faça isso para todas as 3 máquinas.

## Instalação do Ansible

A máquina com o Ubuntu será o control node.

Acesse a máquina Ubuntu via Vagrant: Abra o terminal, navegue até o diretório onde o seu Vagrantfile está localizado e execute o seguinte comando:
vagrant ssh ubuntu

Atualize o gerenciador de pacotes: Assim que estiver conectado à máquina Ubuntu via SSH, execute o seguinte comando para garantir que todos os pacotes estejam atualizados:

```
sudo apt update && sudo apt upgrade -y
```

## Instale o Ansible: O Ansible geralmente não está nos repositórios padrão do Ubuntu, mas pode ser instalado adicionando um repositório externo e, em seguida, instalando-o. Siga estes passos:

a. Adicione o repositório do Ansible:

```
sudo apt-add-repository --yes --update ppa:ansible/ansible
```

b. Atualize novamente os pacotes:

```
sudo apt update
```

c. Instale o Ansible:

```
sudo apt install ansible -y
```

Verifique a instalação do Ansible: Após a instalação, verifique se o Ansible está instalado corretamente e qual versão foi instalada:

```
ansible --version
```

Isso exibirá informações sobre a versão do Ansible instalada na máquina.
Com esses passos, você deverá ter o Ansible instalado e pronto para ser usado na máquina Ubuntu criada pelo Vagrant.

## Configurando o controle das outras máquinas

Edite o arquivo de inventário do Ansible: O inventário do Ansible (hosts) é onde você define quais máquinas serão controladas. Abra ou crie o arquivo de inventário:

```
sudo vim /etc/ansible/hosts
```

Adicione as outras máquinas ao inventário: No arquivo de inventário, adicione as outras máquinas que você deseja controlar. Por exemplo:

```
[centos]
192.168.50.2  # Substitua pelo IP da máquina CentOS

[debian]
192.168.50.3  # Substitua pelo IP da máquina Debian
```

Teste a conexão: Execute o seguinte comando para verificar se o Ansible consegue se conectar às outras máquinas:

```
ansible all -m ping
```

Isso enviará um ping para todas as máquinas listadas no inventário e deverá retornar com um resultado de sucesso (SUCCESS) se as configurações de SSH estiverem corretas.

## Utilizando o Ansible para controle das máquinas

Agora que o Ansible está instalado e configurado, você pode criar playbooks e executar tarefas em várias máquinas. Por exemplo, se você deseja instalar um pacote em todas as máquinas, pode criar um playbook para isso.

## Instalação do Zabbix Agent de forma automatizada por meio do Ansible

Para instalar o Zabbix Agent nas três máquinas (CentOS, Debian e Ubuntu) usando o Ansible, você pode criar um playbook que execute a instalação em cada uma delas.

[zabbix-agent.yaml](https://github.com/andersonvm/lab-ansible-zabbix-grafana-prometheus/blob/main/zabbix-agent.yml)

Este playbook realiza as seguintes etapas:
Verifica o sistema operacional de cada máquina.
Instala o Zabbix Agent de acordo com o sistema operacional de cada máquina.
Inicia e habilita o serviço do Zabbix Agent.
Para executar este playbook, salve o conteúdo acima em um arquivo install_zabbix_agent.yml e execute o seguinte comando no diretório onde o arquivo está localizado:

```
ansible-playbook -i "IP_MAQUINA_1, IP_MAQUINA_2, IP_MAQUINA_3," -u VAGRANT_USER --private-key=PATH_DA_CHAVE_PRIVADA zabbix-agent.yml
```

Substitua IP_MAQUINA_1, IP_MAQUINA_2, IP_MAQUINA_3 pelos endereços IP das máquinas criadas pelo Vagrant. Também substitua VAGRANT_USER pelo usuário Vagrant utilizado e PATH_DA_CHAVE_PRIVADA pelo caminho para a chave privada, se necessário.

Certifique-se de ter o Ansible configurado corretamente e acesso SSH às máquinas através da chave privada para executar o playbook com sucesso.

## Instalação do Zabbix Server no CentOS de forma automatizada por meio do Ansible

Para instalar o Zabbix Server na máquina CentOS usando o Ansible, você pode seguir este exemplo de playbook do Ansible. Certifique-se de ter o Ansible instalado na máquina que será usada para executar este playbook.

Crie um arquivo chamado, por exemplo, install_zabbix.yml com o seguinte conteúdo:

[install_zabbix.yml](https://github.com/andersonvm/lab-ansible-zabbix-grafana-prometheus/blob/main/install_zabbix.yml)

Este playbook realiza as seguintes etapas:

Atualiza o gerenciador de pacotes.
Instala os pré-requisitos do Zabbix Server.
Inicia e habilita o serviço do banco de dados MariaDB.
Cria o banco de dados inicial para o Zabbix.
Importa o esquema SQL do Zabbix.
Configura o arquivo de configuração do Zabbix Server.
Inicia e habilita os serviços do Zabbix Server, Apache e Zabbix Agent.

Para executar este playbook, salve o conteúdo acima em um arquivo install_zabbix.yml e execute o seguinte comando na máquina onde o Ansible está instalado:

```
ansible-playbook -i "IP_DA_MAQUINA," -u VAGRANT_USER --private-key=PATH_DA_CHAVE_PRIVADA install_zabbix.yml
```

Certifique-se de substituir IP_DA_MAQUINA, VAGRANT_USER e PATH_DA_CHAVE_PRIVADA pelos valores corretos. O IP_DA_MAQUINA é o endereço IP da máquina CentOS que você criou com o Vagrant.

## Instalação do Prometheus e Grafana na máquina Debian

Para instalar o Prometheus e o Grafana na máquina Debian usando o Ansible, você pode criar um playbook para realizar essa tarefa.

[install_prometheus_grafana.yml](https://github.com/andersonvm/lab-ansible-zabbix-grafana-prometheus/blob/main/install_prometheus_grafana.yml)

Este playbook realiza as seguintes etapas:

Adiciona a chave do repositório do Prometheus.
Adiciona o repositório do Prometheus ao sistema.
Instala os pacotes do Prometheus e Grafana.
Inicia e habilita os serviços do Prometheus e Grafana.

Para executar este playbook, salve o conteúdo acima em um arquivo install_prometheus_grafana.yml e execute o seguinte comando no diretório onde o arquivo está localizado:

```
ansible-playbook -i "IP_DA_MAQUINA," -u VAGRANT_USER --private-key=PATH_DA_CHAVE_PRIVADA install_prometheus_grafana.yml
```

Substitua IP_DA_MAQUINA pelo endereço IP da máquina Debian criada pelo Vagrant. Substitua também VAGRANT_USER pelo usuário Vagrant utilizado e PATH_DA_CHAVE_PRIVADA pelo caminho para a chave privada, se necessário.

Certifique-se de ter o Ansible configurado corretamente e acesso SSH à máquina Debian através da chave privada para executar o playbook com sucesso.

Aproveite este laboratório para realizar ensaios e verificar o funcionamento de cada ferramenta.
