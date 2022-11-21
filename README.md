#Instalação e configuração do Servidor Samba

Este material tem a finalidade de mostrar como é feita a instalação e configuração do servidor Samba, que serve para a transferência de arquivos entre diferentes sistemas, inclusive, entre sistemas Linux e Windows.

**Observações:**
* O presente documento é um tutorial desenvolvido para o trabalho final da disciplina de Administração de Sistemas Abertos, do curso de Tecnologia em Redes de Computadores, no IFCE Campus Canindé.

* O sistema operacional usado na máquina servidora é o Ubuntu Server.

* **IMPORTANTE:** Todos os comandos precisam da permissão do SuperUser, então, é preciso logar como tal ou usar o comando `sudo` no começo de cada comando abaixo.

##Primeiro passo: Criando um ambiente de teste

Como o tutorial tem a finalidade de mostrar como se faz a instalação e a configuração do serviço/servidor e mostrar se está funcionando ou não, antes de começar a instalação, foi criado um ambiente que parecesse com alguma aplicação real, no caso. 
O ambiente em questão tem uma pasta de arquivos, localizada em `\home` e dentro dessa pasta, há outras duas pastas, a pasta "Estudantes" e a pasta "Professores", pensando em um cenário de uma escola ou qualquer outra instituição de ensino, então, no primeiro momento, é necessário criar esses diretórios (Arquivos, Estudantes e Professores) no diretório `\home`, para isso, pode-se escrever os 3 comandos a seguir em sequência:
*`mkdir \home\Arquivos`
*`mkdir \home\Arquivos\Estudantes`
*`mkdir \home\Arquivos\Professores`

*Os diretórios "Estudantes" e "Professores" estarão dentro do diretório "Arquivos"

##Segundo passo: Definição de grupos e usuários
Após a criação dos diretórios que serão utilizados para essa prática, deverão ser criados os usuários que poderão acessar a esses arquivos, deverão ser criados os grupos aos quais esses usuários pertencem e deve ser estabelecida essa relação.

Então, primeiramente, para criar os usuários, é necessário utilizar os seguintes comandos:

*`adduser --disabled-login --no-create-home aluno1`

*`adduser --disabled-login --no-create-home professor1`

**Observações:** `--disabled-login` e `--no-create-home` são parâmetros que passamos na criação dos usuários, para dizer que o login está desabilitado (no primeiro parâmetro), e para dizer para não criar uma pasta para esse usuário no diretório `\home`
Posteriormente, é necessário criar os dois grupos (alunos e professores), para isso:

*`addgroup alunos`
*`addgroup professores`

Depois de serem criados os grupos e os usuários, é necessário estabelecer a relação entre os dois, através dos comandos:

*`adduser aluno1 alunos`

*`adduser professor1 professores`

##Terceiro passo: Alterar as permissões dos diretórios "Estudantes" e "Professores"

É necessário mudar as permissões dos diretórios, para que as pessoas certas tenham acesso às informações certas.

Para fazer isso, deve-se utilizar os seguintes comandos:
*`chmod 770 /home/Arquivos/Estudantes`

*`chmod 770 /home/Arquivos/Professores`

**Observações:** 770 indica que o arquivo dá todas as permissões para o SuperUser e para o grupo, mas nenhuma permissão para outros

##Quarto passo: Instalação e configurações iniciais do Servidor Samba

* Instalação do pacote Samba:

`apt install samba -y`

**Obs:** está sendo utilizado "apt" para fazer a instalação por estar com uma distribuição Ubuntu; e o parâmetro "-y" é para já ser uma resposta ao momento da instalação em que o instalador pergunta se quer prosseguir e tem como respostas "y" para "Sim" e "n" para "Não"

Depois de instalar o serviço, vamos trocar a senha de autenticação dos usuários que criamos na segunda parte do tutorial.
Para fazer a troca da senha de autenticação em cada um dos usuários, iremos utilizar os comandos:

*`smbpasswd -a aluno1`
*`smbpasswd -a professor1`

Outra pré-configuração para se fazer é a de criar um diretório que servirá como "lixeira", para armazenar tudo o que for descartado do servidor em qualquer máquina cliente, para isso, utilizaremos o comando: 

*`mkdir /home/lixeira`

Depois disso, a última pré-configuração a se fazer é a de criar um arquivo de backup do arquivo de configuração do Samba e mover tudo o que está no arquivo original, para que permaneça como o inicial, para caso seja necessário recuperá-lo, faremos isso com o comando:

*`mv /etc/samba/smb.conf /etc/samba/smb.conf.bkp`


