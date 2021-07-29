 

# File Hierarchy



/bin - binários

​		Executaveis do SO (gitkraken, vscode, ...)

/boot - boot do SO

​		Arquivos responsáveis pelo boot do sistema

/dev - devices

​		Dispositivos conectados (moutns, periféricos)

/etc - Etcetera

​		Configurações adicionais dos sistemas	

​		

/home 

​		Arquivos pessoais dos usuiaiors

/media

​		Montagens de periféricos externos como HD, que são conectados diversas vezes

/mnt

​		Montagens periodicas

/opt - optional

​		

/proc - processos

​		Informações sobre os processos, sistema operacional e Hardware

/root

​		Funcionalidades esecificas do root

/sbin

​		binários do SO

/sys

​		



# Resumo APT+DPKG - RPM - YUM

​	Apt é o administrador de pacotes .deb do sistema GNU/LINUX, por baixo é utilizado o comando dpkg.

​	O apt busca dos servidores listados no */etc/apt/sources* os pacotes .deb



##### Atualização de pacotes 

```bash
apt update	
# -----
yum update
```



##### Atualização Pacotes do Sistema

```bash
apt upgrade	
# -----
yum upgrade
```



##### Instalar um pacote 

```bash
apt install -y XXX
dpkg -i XXX.deb	
# -----
yum install -y XXXX
# -----
rpm -i -hv XXXX.rpm
```

##### Instalar dependencias

```bash
apt-f install
```



##### buscar um pacote

```bash
apt search XXX
apt cache search XXX
```



##### Verificar os instalados

```bash
dpkg -l # | grep XXX
dpkg selections
```



##### Para remover

```bash
apt remove --purge XXXX -y
apt autoremove # remove as libs/.deb dependendtes que não estão sendo utilizadas
# -----
yum remove XXXX
```



# Comandos

#### Comando para buscar o usuário:

- whoami
- echo $USER
- echo 



#### Informações Hardware e SO

- uname -a





# BASH

#### Histórico de comandos bash

$`cat ~/.bash_history`

$ `set | grep HIST`

$ `HISTSIZE=1000  `



#### Atalhos bash

$ `history`

$ `$ !s` Executa o ultimo comando executado coma  incial "s"

$ `$ !123` Executa o comando n 123 do "history"



#### Arquivo .bashrc

 .bashrc é o arquivo com toda a execução do bash.



#### Pré Bash - .bash_profile + .profile + login interativo shell

login interativo shell é a execução do shell por login, sem interface gráfica. Exemplos: tty, ssh

O login interativo vai executar uma ordem diferente de arquivos. Sendo ela:

​	/etc/profile

​	~/.bash_profile OU ~/.bash_login OU ~/.profile          O que encontrar primeiro



# Variaveis Globais e Locais

 env = Environment -> Globais

set = Locais



# File Globbing

##### Qualquer Coisa *

```bash
ls *.txt # Retorna qualquer Arquivo que termine com .txt
```



##### Caractere coringa

```bash
ls ?.txt # Substitui o "?" por qualquer letra -> a.txt, b.txt
```



##### Lista de caracteres

```bash
ls [Rr]eports*.txt # Reports.txt ou reports.txt ou reports1998-08-02.txt
```



##### Lista Negada

```bash
ls [^fFrR]* # Todos que não começam com f F r R
```



# Quotting " "



Aspas duplas " " identificam caracteres especiais $, * ...

```bash
echo "O usuário logado é o $LOGNAME"
#	O usuário logado é o Felipe

echo "O usuário logado é o \$LOGNAME"
#	O usuário logado é o $LOGNAME
```



Aspas simples não interpretam caracteres especiais

```bash
echo 'O usuário logado é o $LOGNAME'
#	O usuário logado é o $LOGNAME
```



# Detalhes do terminal

$ -> Usuario comum

\# -> super usuario



{comando} {parametro} {argumento}

ls -l .config/



### Encontrar diretorios e arquivos

##### $ locate

Utiliza a base de dados "updatedb" para rastrear todos os arquivos. Essa base de dados é carregada apenas quando o OS inciailiza

```bash
locate {file} 
```

Para atualizar a base de dados

```bash
sudo updatedb
```





##### $ find

 Busca todos os arquivos dentro do diretório .config com o nome terminando em .xml

```bash
find .config/ --iname *.xml
```



##### $ type

Comando para especificar o comando

```bash
type ls
# ls está apelidada para `ls --color=auto`
```



##### $ whereis

```bash
whereis firefox
```



# Documentações e manuais

```bash
$ man -l
$ cd /usr/shate/doc

# Para ler os arquivos compáctador utulizar o comando
$ man -l arq.gz

# Profile de configuração
cat /usr/shate/man/pt_BR/man1/etc/profile
```



# Compactar e Comprimir

Compactar X Comprimir

| Comando | Prós                        | Comentario                         | Comentario 2                     |
| ------- | --------------------------- | ---------------------------------- | -------------------------------- |
| tar     | Mais usual                  | Apenas compactador no comando puro | tar -cz = gzip \| tar -cj = bzip |
| zip     | Multiplataforma (windows)   | Não otimiza espaço na compressão   |                                  |
| xz      | Melhor otimização de espaço |                                    |                                  |



#### $ tar

Compactar junta todos os arquivos em uma pasta sem necessariamente diminuir o tamanho

Comprimir compacta os arquivos e diminui o tamanho utilizando um algoritmo



```bash
tar cvf {file.tar} /etc
# Compacta o etc para file.tar

tar cvfz {file.tar.gz} /etc
# Comprime o /etc ocupando MUITO menos espaço
# gzip file.tar.gz /etc

tar cvfj {file.tar.bz2} /etc
# Comprime o /etc ocupando menos espaço que o .gz
# bunzip2 file.tar.bz2 /etc
```



```bash
tar xvf file.tar
# Descompacta o file.tar

tar xjvf file.tar.bz2
# Descompacta o file.tar
# bunzip2 file.tar.bz2

tar xzvf file.tar
# Descompacta o file.tar.gz
# gunzip file.tar.gz
```

 Por tras os comandos "tar -cz " "tar -cj" são chamados os comandos gzip e bzip2

Os comandos "tar -xz" "tar -xj" chamam os comandos gunzip e bunzip2





#### $ zip

Não otimiza tamanho

```bash
zip -r file.zip /etc
unzip file.zip
```



#### $ xz

Melhor aprovetamento de armazenamento

```bash
xz /etc
unxz etc.xz
```





# Analise de arquivos/logs

#### $ less

#### $ head -n {numero de linhas}

#### $ tail -n {numero de linhas}



# Redirecionamentos de comandos

##### Standart In

* stdin 
  * Toda entrada no terminal (ls, echo, mkdir,...)

##### Standart Out 

* stdout

  * Toda saída sucesso do terminal (echo)

    

```bash
echo "Felipe" 
# Felipe --> saída sucesso
```



##### Standart Error 

* stderror 
  * Toda saída Erro do terminal 

```bash
apt update
# Reading package lists... Done
# E: Could not open lock file /var/lib/apt/lists/lock - open (13: Permission denied)
# E: Unable to lock directory /var/lib/apt/lists/
# W: Problem unlinking the file /var/cache/apt/pkgcache.bin - RemoveCaches (13: Permission denied)
# W: Problem unlinking the file /var/cache/apt/srcpkgcache.bin - RemoveCaches (13: Permission denied)

```



| Standart | Comando                              |
| -------- | ------------------------------------ |
| STDOUT   | 1>           1>>         >        >> |
| STDERROR | 2>           2>>                     |





# Ciclo de vida Distribuições



|             | Red Hat Ent Linux | Fedora | Suse     | OpenSuse | Debian   | Ubuntu |
| ----------- | ----------------- | ------ | -------- | -------- | -------- | ------ |
| C.V         | 10 anos           | 1 ano  | 7 anos   | 18 mes   | 3-4 anos | 5 anos |
| C.A (Patch) | 3-4 ano           | 6 mes  | 3-4 anos | 8 mes    | 2 anos   | 6 anos |





# Informações de hardware

 

| Componente            | Comando para visualizar dados        |
| --------------------- | ------------------------------------ |
| Processador           | cat /proc/cpuinfo                    |
| Memoria               | free -h                              |
| Placa mãe             | dmidecode -t baseboard               |
| Fonte                 |                                      |
| Disco / Armazenamento | $ fdisk -l<br />$ lsblk<br />$ df -h |



# Gerenciamento de processos

```bash
ps
ps -u username
ps --all
top --> k (kill)
```

 



# Gerenciamento de logs

#### Arquivo de configuração dos logs.

Esse arquivo monitora todas as mensagens do sistema e envia para seus respectivos files de log de acordo com a configuração desse arquivo

##### 	/etc/rsyslog.d/50-default.conf



#### Logs do OS são encontrados no diretório

##### 	/var/log/*

Cada file é tem uma responsabilidade especifica



Para os logs gerais do Kernel pode acessar

```bash
cat /var/log/kern.log
```



#### Dump da memoria

```bash
dmesg
```





# Redes

```bash
## IP
ip addr show
ifconfig
## Rotas
iproute
route
netstat -r
## Portas
netstat -tulpn | grep LISTEN
ss -ln
## Resolução de nomes / DNS
cat /etc/resolv.conf
cat /etc/hosts
```





# Segurança Básica e Identificação de tipos de usuário

 

#### Usuários conectados

```bash
## Usuários conectados
who
w
last # histórico de sessões
last reboot # histórico de reboot 

```



#### Arquivo de permissões de usuários para sudo 

* ##### /etc/sudoers

#### Arquivo de grupos de usuários

* ##### /etc/group  -> | grep username

#### Usuarios

* ##### /etc/passwd 

#### Senhas - Configuração de expiração de senhas

* ##### /etc/shadow



# Criar usuários e grupos

#### Skeleton - Esqueleto

Arquivo de configuração do /home de todos os novos usuários. Esse arquivo define quais arquivos padrões mínimos os usuarios devem ter no seu /home

* ##### /etc/skel

#### Criar grupo

```bash
groupadd {Nome Grupo}
```

#### Criar usuários

```bash
$ useradd -G 1003 -m -c "Felipe Grings" felipe
# {comando} -G {grupo complementar} -m (Criar /home) -c {Comentario} {nome do usuario}

# Verificar a criação
$ cat /etc/passwd | grep felipe

# Alterar configurações do usuarios
$ usermod --help

# Adicionar senha
$ passwd felipe
```



#### Configuração de inicialização de novos Usuarios (Shell, /home, skel,...)

* #### /etc/default/useradd





# Permissões de arquivos e ownership

#### Permissões 

- {R}ead - 4
- {W}rite - 2
- E{x}ecute - 1

#### Grupos de permissões e Escrita numérica

| A quem pertence             | Dono do Arquivo | Grupo do Dono do arquivo | Resto dos usuarios |
| :-------------------------- | :-------------: | :----------------------: | :----------------: |
| Completa                    |       rw-       |           rw-            |        r-x         |
| Numerica (soma dos valores) |        6        |            6             |         5          |

#### 		

#### Comandos para manipulação

```bash
# Permissão de Arquivo
chmod o+rwx u+rwx g+rwx file.sh # o=rw | o-r
# o (others) u (user) g (group)
chmod 777 file.sh
# -R para recursividade Ex.: chmod 777 -R files/

# Permissão de proprietario
chown felipe:financeiro file3.sh
# {usuario:grupo}

# Alterar apenas o grupo
chgrp financeiro file2.sh
```





# Link simbólico x Link Físico

#### Estruturação de dados linux

Os arquivos são alocados em memória localizados Inodes. Cada Inode deve conter 1 conexão para exister.

Link simbolico gera um novo Inode apontando para o Inode do arquivo apontado

Link Fisico apresenta um novo file apontando exatamente para o mesmo Inode



#### Representação grafica

![image-20200729191000503](/home/teste/.config/Typora/typora-user-images/image-20200729191000503.png)

#### Código exemplo

```bash
touch teste
ln -s teste linkSymb # Gera um link simbólico com o teste
ls -l
ls- i
rm teste
touch teste
ln teste linkHard # HardLink
ls -i
ln linkSymb ShouldBeHard # Gera um link simbólico, pois o link hard ta apontando para um link simb
ls -l
```



#### Permissão especial Stick Bit

Permissão para diretórios onde apenas o dono do diretório e quem criou o arquivo tem permissão de remover.

Para adicionar a permissão Stick Bit existem as seguinte maneiras:

```bash
# Para Adicionar
chmod 1777 dirTeste # 1 é repsonsavel por ativar o Stick Bit
chmod o+t dirTeste # t = s{t}ick bit

# Para remover
chmod 0777 dirTeste
chmod o-t dirTeste
```

