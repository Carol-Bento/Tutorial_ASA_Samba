# Instalação e configuração do Servidor Samba

`Desenvolvido por: Caroline Bento, Evaldo Benevenuto e Cristóvão Wesley, da turma de 4° semestre em Tecnologia em Redes de Computadores`

Este material tem a finalidade de mostrar como é feita a instalação e configuração do servidor Samba, que serve para a transferência de arquivos entre diferentes sistemas, inclusive, entre sistemas Linux e Windows.

**Observações:**
* O presente documento é um tutorial desenvolvido para o trabalho final da disciplina de Administração de Sistemas Abertos, do curso de Tecnologia em Redes de Computadores, no IFCE Campus Canindé.

* O sistema operacional usado na máquina servidora é o Ubuntu Server.

* **IMPORTANTE:** Todos os comandos precisam da permissão do SuperUser/root, então, é preciso logar como tal ou usar o comando `sudo` no começo de cada comando abaixo.

## Primeiro passo: Criando um ambiente de teste

Como o tutorial tem a finalidade de mostrar como se faz a instalação e a configuração do serviço/servidor e mostrar se está funcionando ou não, antes de começar a instalação, foi criado um ambiente que parecesse com alguma aplicação real, no caso. 
O ambiente em questão tem uma pasta de arquivos, localizada em `\home` e dentro dessa pasta, há outras duas pastas, a pasta "Estudantes" e a pasta "Professores", pensando em um cenário de uma escola ou qualquer outra instituição de ensino, então, no primeiro momento, é necessário criar esses diretórios (Arquivos, Estudantes e Professores) no diretório `\home`, para isso, pode-se escrever os 3 comandos a seguir em sequência:
* `mkdir \home\Arquivos`
* `mkdir \home\Arquivos\Estudantes`
* `mkdir \home\Arquivos\Professores`

* Os diretórios "Estudantes" e "Professores" estarão dentro do diretório "Arquivos"

## Segundo passo: Definição de grupos e usuários
Após a criação dos diretórios que serão utilizados para essa prática, deverão ser criados os usuários que poderão acessar a esses arquivos, deverão ser criados os grupos aos quais esses usuários pertencem e deve ser estabelecida essa relação.

Então, primeiramente, para criar os usuários, é necessário utilizar os seguintes comandos:

* `adduser --disabled-login --no-create-home aluno1`

* `adduser --disabled-login --no-create-home professor1`

**Observações:** `--disabled-login` e `--no-create-home` são parâmetros que passamos na criação dos usuários, para dizer que o login está desabilitado (no primeiro parâmetro), e para dizer para não criar uma pasta para esse usuário no diretório `\home`
Posteriormente, é necessário criar os dois grupos (alunos e professores), para isso:

* `addgroup alunos`
* `addgroup professores`

Depois de serem criados os grupos e os usuários, é necessário estabelecer a relação entre os dois, através dos comandos:

*`adduser aluno1 alunos`

*`adduser professor1 professores`

É necessário também fazer a modificação dos grupos que terão acesso às pastas de arquivos, para isso:
* Mude seu diretório e vá para `/home/Arquivos`, com o comando:

* `cd /home/Arquivos`

* Posteriormente, verifique todos os arquivos e diretórios que têm lá, com informações sobre grupos e permissões com o comando:

* `ls -l`

* Você irá verificar que ambas as pastas, Estudantes e Professores, estão com o grupo proprietário "root", para mudar o grupo, digite os comandos:

* `chgrp alunos Estudantes/`
* `chgrp professores Professores/`

## Terceiro passo: Alterar as permissões dos diretórios "Estudantes" e "Professores"

É necessário mudar as permissões dos diretórios, para que as pessoas certas tenham acesso às informações certas.

Para fazer isso, deve-se utilizar os seguintes comandos:
*`chmod 770 /home/Arquivos/Estudantes`

*`chmod 770 /home/Arquivos/Professores`

**Observações:** 770 indica que o arquivo dá todas as permissões para o SuperUser e para o grupo, mas nenhuma permissão para outros

## Quarto passo: Instalação e configurações iniciais do Servidor Samba

* Instalação do pacote Samba:

* `apt install samba -y`

**Observações:** 
* Está sendo utilizado "apt" para fazer a instalação por estar com uma distribuição Ubuntu; 
* e o parâmetro "-y" é para já ser uma resposta ao momento da instalação em que o instalador pergunta se quer prosseguir e tem como respostas "y" para "Sim" e "n" para "Não"

Depois de instalar o serviço, vamos trocar a senha de autenticação dos usuários que criamos na segunda parte do tutorial.
Para fazer a troca da senha de autenticação em cada um dos usuários, iremos utilizar os comandos:

* `smbpasswd -a aluno1`
* `smbpasswd -a professor1`

Outra pré-configuração para se fazer é a de criar um diretório que servirá como "lixeira", para armazenar tudo o que for descartado do servidor em qualquer máquina cliente, para isso, utilizaremos o comando: 

* `mkdir /home/lixeira`

Depois disso, a última pré-configuração a se fazer é a de criar um arquivo de backup do arquivo de configuração do Samba e mover tudo o que está no arquivo original, para que permaneça como o inicial, para caso seja necessário recuperá-lo, faremos isso com o comando:

* `mv /etc/samba/smb.conf /etc/samba/smb.conf.bkp`

## Quinto passo: Configuração do servidor

Para configurar o servidor Samba, é necessário fazer a configuração do arquivo Samba, que está no direório `/etc/samba/smb.conf`, para isso, é necessário abrir o arquivo em algum editor de texto, como o Vim ou Vi, ou Nano, nesse caso, utilizarei o Vi, então, digitarei o comando:

* `vi /etc/samba/smb.conf`

Após executado, esse comando abrirá a tela do editor de texto, que estará vazio, exceto por várias linhas contendo apenas "~", então, ao apertar a letra "i" ou "o", é possível fazer modificações no arquivo, e a partir de então, deverão ser escritas as seguintes linhas: 

* `[global]`
*   `netbios name = ServidorArquivos`
*   `workgroup = WORKGROUP`
*   `server string = Servidor de Arquivos da Escola`
*   `security = user`
*   `encrypt passwords = yes`
*   `invalid users = root`

* `[arquivos]`
*   `path = /home/Arquivos`
*   `writable = yes`
*   `available = yes`
*   `browseable = yes`
*   `valid users = @alunos, @professores`
*   `comment = Servidor de Arquivos da Escola`
*   `vfs object = recycle`
*   `recycle:repositor = /home/lixeira`
*   `recycle:keeptree = yes`
*   `recycle:exclud = *.tmp`
*   `recycle:exclud_dir = cache, tmp`

Posteriormente, **para salvar tudo**, é só apertar a tecla `ESC` e digitar `:wq`


**Observações:**
* `[global]` trata-se da configuração geral do servidor 
* o "Workgroup" foi definido como "WORKGROUP" por que vou testar o servidor num cliente windows, e esse é o nome padrão de workgroup no windows
* `[arquivos]` é o identificador da pasta de arquivos que será compartilhada

Depois de finalizada a configuração, reinicialize o serviço, com o comando: 
* `service smdb restart`

Posteriormente, verifique se o serviço está funcionando corretamente, com o comando:
* `service smdb status`


## Sexto passo: Conectando um cliente Windows com o Servidor Samba

Para conectar um cliente Windows com o servidor é muito fácil, primeiramente você vai pegar o endereço de ip da máquina servidora, com o comando:
* `ip address`

Depois, vamos no Windows  Explorer, e na barra de pesquisa/de diretório, digite o IP da máquina servidora, depois escolha uma das pastas de arquivos (Estudantes ou Professores) e coloque a senha.
