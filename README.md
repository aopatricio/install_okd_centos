# Portas requeridas para Cluster Openshift

As portas abaixo precisão esta liberadas no firewall/Servidor para o funcionamento do Cluster Openshift.
```
22 			TCP
53 or 8053		TCP/UDP
80 or 443		TCP
1936			TCP
4001			TCP
2379 and 2380		TCP
4789			UDP
8443			TCP
10250			TCP
```
Para maiores detalhes sobre quais portas são necessárias consulte a documentação no link https://docs.openshift.com/container-platform/3.11/install/prerequisites.html


# Instalação Openshift 3.11 no CentOS 7.6
Para esse laboratório foram provisionadas na AWS 3 VMs ec2 com o CenOS Instalados

Habilitar acesso root nas VMs em /etc/ssh/sshd_config file. 

```
$ sudo vi /etc/ssh/sshd_config
PermitRootLogin yes    	#Uncomment this line in /etc/ssh/sshd_config and save it.
	 
$ sudo systemctl restart sshd
```

Por padrão o IAM não permite logar como *root* o usuario em umam instância EC2 mas você pode usar o authroized_keys do *centos* para isso.

```
$ sudo -s
$ cp /home/centos/.ssh/authorized_keys /root/.ssh/authorized_keys
```

Pronto... agora você esta pronto para fazer logon como root em uma instância EC2. Lembre-se de executar o mesmo procedimento em cada uma das VMs que pertencerá ao Cluster OpenShift

# Instalação do git e procedimento de clone do conteudo no GitHube. Essa etapa pode ser feita diretamente no master ou no servidor de Bastian

```
$ yum install -y git
$ git clone https://github.com/aopatricio/openshift-centos.git
$ cd openshift-centos
```

**Note:** Se após o clone o script de requisitos apresentar caracteres bagunçado execute o comando abaixo para cada caracter errado.

```
$ sed -i -e 's/\r$//' install-tools.sh
```


Não esqueça de dar permissão de execução para o scritp.

*install-tools.sh* esse script irá fazer a instalação de todos os pacotes necessário bem como a configuração do parâmetro da placa de rede. Todas essas configurações são necessárias para instalação do Cluster Openshift. Lembre-se de executa-lo como root.

```
$ chmod +x install-tools.sh
$ ./install-tools.sh
```

Copie *install-tools.sh* para todas as VMs que fazem parte do Cluster OpenShift 3.11 e executá-lo em cada uma das VMs.

```
#To ssh from one node to another using .pem file
$ scp -i keypair.pem install-tools.sh  root@host_ip:~/
$ ssh -i Openshift-keypair.pem root@host_ip
$ ./install-tools.sh
```


## Atualize DOMAIN_NAME no arquivo de inventário.


* Aqui temos multplos arquivo de inventario , escolha o que melhor lhe atendá e renomeie/copie para um novo arquivo chamado inventory.ini*

Substitua o campo "your_domain_name" para o nome do seu dominio
```
openshift_public_hostname=console.your_domain_name
openshift_master_default_subdomain=apps.your_domain_name
```

Pronto, após realizar todas as alterações aqui mensionadas agora é hora de iniciar o processo de instalação do CLuster OpenShift. Execute o comando abaixo no Master ou no servidor Bastian:

```
$ chmod +x install-openshift.sh
$ ./install-openshift.sh
```

Se tudo correr direitinho, ao final basta acessar a console pela seguinte url  **https://console.your_domain_name**
