### 101.3 Alterar os RunLevels/Boot targers. Shutdown e Reboot (3)

* SystemV (SysV) - init original

  * Trabalha em base nos RunLevels
    * RunLevel 0 - Desligado
    * RunLevel 1 - Configurações (Sem interface e conexão internet)
    * RunLevel 2, 3, 4, 5 - Multi Usuário (Normal)
    * RunLevel 6 - Reboot
  * Alterar o estado de run Level
    * init {estado}
    * telinit {estado}

* systemd - Gerenciador de sistemas e serviçõs compaivel com o SysV

  * O SystemD, é o gerenciador mais utilizado (systemctl) 
  * É baseado em unidades (serviços) que são agrupos em targets
    * service
    * socket
    * ...
  * Os targets são equivalentes aos RunLevels e linkados na pasta **/lib/systemd/system**

  * Comandos Uteis

  ```bash
  systemctl list-units
  systemctl poweroff/reboot
  systemctl set-default multi-user.target # Atualiza o link do default target
  systemctl isolate rescue.target # Modo 1
  systemctl default # Inicia o target default
  ```

  

* Upstart - Gerenciador de serviços subitituto ao init, mas com opções de compatibilidade

  * start {service}



### 102.2 Instalação do Boot Manager (2)

#### GRUB - É o multicarregador de sistema operacional ( Tela inicial do F12 )

|                          | GRUB LEGACY                                                  | GRUB2                                                        |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Arquivos de configuração | **/boot/grub/menu.lst**<br />/boot/grub/grub.cong/cnf        | **/boot/grub/grub.cgf**<br />/etc/default/grub<br />/etc/grub.d |
| Referencia ao disco      | hda1 = **hd0,0**<br />hda5 = hda0,4<br />hd3 = hda1,2        | hda1 = **hd0,1 ou hd0,msdos1**<br />hda5 = hda0,5<br />hd3 = hda1,3 |
| Comandos                 | grub-install /dev/sda                                        | grub-install <device><br />update-grub<br />grub-mkconfig -o<br />/boot/grub/grub.cfg |
| Principais Parametros    | title "Ubuntu"<br />**root(hd0,0)<br />kernel /boot/vmlinuz-4-8.20-93<br />**generic ro root=/dev/sda5 | menuentry "Ubuntu" **{<br />set root=(hd0,1)<br />linux** /boot/mvlinux-4-5.182<br />} |
|                          |                                                              |                                                              |



### 102.4 Gerenciamento de Pacotes Debian (3)**Comando rpm**

```bash
dpkg 
	-i					# Install
    -I {pacote}  	 	# Informações sobre o pacote
    --content {pacote}	# Conteudo
    -l  	 		 	# Lista todos os pacotes intalados
    -L  	 			# Lista de instalados pelo pacote
    -s 		 			# Status
    -S {arquivo}		# Search
    -r {pacote}			# Remove, mantem na base de dados como desisntalado
    -P ou --purge		# Remove tudo
    
dpkg-reconfigure

dselect # UI dpkg
```



**Comando apt-get**

```bash
apt-cache
		pkgnames 		# Lista todos os nomes dos pacotes instalados
		show {pacote}	# Informações do pacote
        depends {pkg}	# Pacote depende ..
        
apt-get 
     install 	  # Instala um ou mais pacotes
     remove 	  # Remove um ou mais pacotes
     purge 		  # Lista os pacotes instalados ou disponíveis
     update 	  # Atualiza um ou todos os pacotes
     upgrade 	  # Atualiza um ou todos os pacotes, inclusive removendo ou substituindo pacotes obsoletos
     search 	  # Procura por um pacote baseado em uma palavra/string
     --download-only install
     
 
/etc/apt/source.list # URL de busca dos pacotes

```



### 102.5 Gerenciamento de Pacotes RPM e YUM (3)

**Principais Opções dos Comandos Utilizados no Gerenciamento de Pacotes RPM**

**Comando rpm**

```bash
rpm 
    -i  	 # Instala um pacote. Normalmente utilizado com -ivh
    -U  	 # Atualiza ou instala um pacote. Também normalmente utilizado com -Uvh
    -F  	 # Atualiza um pacote apenas se ele já estiver instalado.
    -e  	 # Remove um pacote
    -qa 	 # Consulta todos os pacotes instalados
    -qi 	 # Consulta um pacote específico
    -ql 	 # Lista todos os arquivos de um pacote
    -qf 	 # Indica o pacote relacionado a determinado arquivo
    -qp 	 # Analisa um pacote .rpm não instalado (-qlp, -qip)
    -V  	 # Verifica a integridade de um pacote
    --force  # permite a substituição de arquivos existentes. 
    --nodeps # não verifica dependências. 
    --test   # não instala efetivamente
```



**Comando yum**

```bash
yum 
     install 	  # Instala um ou mais pacotes
     remove/erase # Remove um ou mais pacotes
     list 		  # Lista os pacotes instalados ou disponíveis
     update 	  # Atualiza um ou todos os pacotes
     check-update # Verifica as atualizações de pacotes disponíveis
     upgrade 	  # Atualiza um ou todos os pacotes, inclusive removendo ou substituindo pacotes obsoletos
     search 	  # Procura por um pacote baseado em uma palavra/string

```

**Principais Arquivos de Configuração**

- /etc/yum.conf
- /etc/yum.repos.d/



#### Variaveis

```bash
set | less #-> saida para toads as variaveis do sistema junto com as exportadas
env | less #-> todas as variaveis do bash
unset VARIAVEL #-> Remove a variavel
$$ #-> PID do processo atual
$! #-> PID do utlimo processo executado em background
$? #-> Exit code do ultimo processo
```





#### Comandos de Ajuda

```bash
man {comando}
man -k "system information"
info {comando}
whatis {comando} # Retorna a descrição do comando
apropos # Retorna o comando baseado na busca
```



#### Comandos importantes

```bash
uname # Informações do sistema
uname -a # All
uname -s # Kernal name
uname -r # Kernel release

##
alias # Atalhos
alias lt='ls /tmp' # Adiciona o atalho lt para o comando ls /tmp    ---DE FORMA TEMPORARIA

##
which {comando} # Localiza onde está o comando
```

```bash
uniq {Arquivo} ## Lista apenas as ocorrencias unicas EM SEQUENCIA
	 # -c -> Printa a quantidade de vezes enquantrada 
	 # -d -> Printa apenas as saida duplicadas
sort arquivo.txt | uniq ## Ordena de forma as ocorrencias iguais ficarem juntas.

od {Arquivo}  ## Printa a saida octa decimal

join {Arquivo} ## Relaciona os dois arquivos. PRECISA ESTAR ORDENADO CORRETAMENTE
	# -j2 -> Usa a segunda coluna como parametro para relação

paste {Arquivo1} {Arquivo2}  ## Printa linha a linha dos dois arquivos

split -20 arquivolongo.txt 		 ## A cada 20 linhas gera um novo arquivo com o conteudo
						 # {Nome Arquivo Saida} -> Gera os arquivo com o nome novo_arquivo-{aa,ab,ac,...}
						 
#####

tr # Transale or delete caracteres
   # [:upper:] [:lower:] -> Transforma tudo de maiusculo para minusculo
   # ' ' '_' -> Transforma espaços vazios em undernline
   # -d A -> Deleta todos os caracteres A
   # -s {caractere} -> Remove caracteres repetidos em sequencia
cat {Arquivo} | tr ei EI # Altera todos os caracteres "e" e "i" por "E" e "I", independentes

cut # Recorta a entrada
	# -c1-5 -> Recorta do caracter 1 ao 5
	# -c1,2,5 -> Recorta caracter 1,2 e 5
	# -d "-" -f1 -> Delimitador "-" e mostra o campo 1
	
sed # Stream editor -> Edita as entradas
	# 's/{Procurado}/{Substituto}' -> Substitui 1' da linha
	# 's/{Procurado}/{Substituto}/g' -> Substitui todas as ocorrencias
	# '3,5 d' -> Deleta da linha 3 a 5
	# '/{Procurado}/d' -> Apaga a linha que conter procurado

```

#### Visualização de aqruivos compactados

```bash
xzcat # Leitura de arquivo xz
bzcat # Leitura de arquivo bz
zcat # Leitura de arquivo gz
```



#### Comandos de checksum

```bash
md5sum {Arquivo} # Gera um hash/checksum do arquivo usando o algoritmo md5
sha265sum {Arquivo} # Gera um hash/checksum do arquivo usando o algoritmo sha256
sha512sum {Arquivo} # Gera um hash/checksum do arquivo usando o algoritmo sha512
		 # -c {Arquivo com os hashs} -> Verifica se os hash do arquivo texto batem com os arquivos do diretório atual
```

```bash
cp  # Copy
	# -i -> Pergunta sobreescrita
	# -v -> Verbose
	# -r -> Recursivo (Diretórios)
	# -p -> Preserve caracteristics

mv  #  Igual /\

rm  # Remover Igual /\

rmdir # Remove apenas diretórios vazios

mkdir # Make Directory -> Cria diretórios
	  # -p -> Cria a arvore Parents -> Ex.: mkdir /root/novo_dir1/ novo_dir1

touch # "Toque" Modifica as caracteristicas do utlimo acesso e modificuação
	  # -a -> Modifica data ultima acesso
	  # -m -> Modifica data ultima modificiação
	  # -t 201712311000 -> Altera para a data 2017 12 31 - 10:00
	  

find {Dir} # Busca 
	 # -name {Nome_arq} -> Busca o arquivo no diretório
	 # -user {username} -> Busca por arquivos com o user
	 # -ctime -1 -> Arquivo com change time de no maximo -1
```

#### Compactação de arquivos

```bash
tar # Não compacta, apenas agrupa. Se usar -z compacta com Gzip
	# -c -> Create
	# -v -> Verbose
	# -f -> File name
	# -p -> Permissions Mantaining
	# -z -> Gzip
	# -j -> Bzip
	# -J -> XZ
tar -cvzf Backup.tar *.log

gzip # Compactação GZIP
	 # -k -> Mantem o arquivo de origem

bzip # Compactação BZIP
	 # -k -> Mantem o arquivo de origem

xz   # Compactação GZIP
	 # -k -> Mantem o arquivo de origem
	 
cpio # Copy File To Archives -> Agrupador, igual o tar puro
	 # -o -> Output. Exporta a saida para um arvuio
find ./ -name "novo*" | cpio -o > backup.cpio
cpio -i < backup.cpio # Restore backup

find ./ -name "novo*" | cpio -o | gzip > backup.cpio
gunzip -c backup.cpio.gz | cpio -i 


dd  # Copia de partição por completo - byte a byte
	if=/dev/sr0 of=imagem.img # if = Input File ; of = Output File
```



#### Redirecionamentos

```bash
tee # Printar o resultado na tela e redirecionar a saída para um arquivo
	-a # Append - Não sobrescreve
sort {Arquivo} | uniq | tee resultado.txt


xargs # Redireciona a saida do ultimo como entrada do a ser executado
ls | xargs rm -v
find /home -name "Teste*" | xargs rm -v
```

### 103.5 Criar, Monitorar e Encerrar Processos (4)

| Nome | Descrição         |
| ---- | ----------------- |
| PID  | Process ID        |
| PPID | Parent Process ID |
| UID  | User ID           |

#### Comandos Básicos

Comando PS é nativo de diversas distros, onde, nos primordios, cada uma tinha a sua forma de utilização. Hoje todas são aceitas, porem com seus respectivos prefixos

```bash
ps  # Snapshot of the current Process
	# -u -> User datas
	# -x -> Processos independentes do terminal
	# -a -> All
	# -f -> Father : Pai/Arvore
	# -C {Nome} -> Lista todos os processos do nome

pstree # Mostra todos os processos em formato de arvore

pgrep # Busca por processos pelo nome ou outro atributo
pgrep bash -u root # Busca o id do bash do usuário root


top #
	# -b

kill # Enviar comando para o processo
killall # Enviar comando para todos os processos X
pkill # Enviar comando 

uptime # Tempo desde o start da maquina
free   # Memoria Total - Utilizada e disponivel 
screen # Diversos bash em uma mesma janela
# ctrl + a + C -> Novo client bash
# ctrl + a + n -> Change to next client
# ctrl + a + d -> Deatach


jobs # Mostra os status dos jobs (Processos iniciados pelo shell)
	 # -l -> List com PID's
bg {1,2,3}  # Passa o ultimo processo (identificado pelo "+" no jobs) para background
fg {1,2,3}	# Passa o ultimo processo (identificado pelo "+" no jobs) para ForeGround

nohup {Processo} # Faz com que o processo ignore o comando NoHup


watch {script} # Comando monitorar o script desejado
			   # A cada 2s reexecuta o comando e printa a saida
			   # -n60 -> Reexecuta a cada 60s


tmux    # Gerenciador de paineis e telas shell - Parecido com screen

        # ctrl+b + C -> Create janela
        # ctrl+b + P -> Previus, passa para a janela anterior
        # ctrl+b + N -> Next, passa para a proxima janela
        # ctrl+b + W -> Lista todas as janelas

        # ctrl+b + shift+5 -> Cria um novo painel vertical
        # ctrl+b + shift+" -> Cria um novo painel horizontal
        
        # ctrl+b + D -> Deatach
        # ls -> List sessions
        # attach -t 0 -> Atacha na sessão 0
        # new -s connection -> Cria uma nova sessão
        # kill-session -t 0 -> Mata a sessão 0
        


```



#### Usos

```bash
nice -n 15 firefox & # Inicia o firefox com NI = 15
	 -15  ... 		 #
     --15 ...		 # Inicia o processo com NI -15
     
renice -n -7 PID	 # Altera o valor de um processo NI para -7
	   -7  ...		 # Altera o valor de NI para -7
	    7  ... 		 # Altera o valor para 7
	    
	   -n 5 -u felipe # Altera todos os processos do user felipe para 5
	   -n 5 -g felipe # Altera todos os processos do grupo
```

### 103.7 Pesquisar Arquivos de Texto Usando Expressões Regulares (3)

#### Comandos básicos 

```bash
grep Linux linux.txt # Get repetitions : Palavra Linux no arquivo linux.txt
	 # -c      -> Count : Retorna a quantidade de vezes que encontrou a palavra
	 # Linux * -> Busca a palavra em todos os arquivos do diretórios
	 # -i 	   -> Ignore case sensitive
	 # -l 	   -> Files with matches
	 # -r 	   -> Recursivo
	 # -E 	   -> Expressões regulares
	 # -v 	   -> Invert Match = regex
egrep # /\	 
fgrep # Ignora todas os caracteres especiais

```

#### Ancora de Posição

| Expressão Reglar | Descrição                                                    | Exemplo   |
| ---------------- | ------------------------------------------------------------ | --------- |
| ^                | Indica o COMEÇO da linh. O caractere deve estar do lado esquerdo da expressão | ^Linux    |
| $                | Indica FINAL da linha. O caractere deve estar do lado direito da expressão | Linux$    |
| /b               | Indica o começo ou fim da palavra (letras, Numero e _)       | /bLinux/b |



#### Definição de caracteres

| Expressão Reglar | Descrição                                                    | Exemplo                               |
| ---------------- | ------------------------------------------------------------ | ------------------------------------- |
| [abc]            | Conjuntos de caractere-unic. Qualquer caractere dentro da lista | [Oo]la = ola, Ola                     |
| \[a-z][0-9]      | Range de caractere-unico. Qualquer caractere dentro do range | Versao[5-7] = Versao5,Versao6,Versao7 |
| \[^abc]\[^0-9]   | Negacao da lista                                             | Versao\[^0-7]= Versao8, Versao9       |



#### Qunatificadores

| Expressão Reglar | Descrição                                              | Exemplo                         |
| ---------------- | ------------------------------------------------------ | ------------------------------- |
| .                | Indica Qualquer caractere unico                        | Bo.a = Bola, Bora, Boca,...     |
| *                | Nenhuma ou varias ocorrencias do caractere anterior    | Bola* = Bol, Bola, Bolaaa,...   |
| +                | Uma ou varias ocorrencias do caractere anterior        | Bola+ = Bola, Bolaaaa           |
| ?                | Nenhuma ou apenas uma ocorrencia do car anteior        | Bola? = Bola, Bol               |
| {n.m}            | Define quantas vezes o caractere anterior deve occorer | Bola{1,3} = Bola, Bolaa, Bolaaa |



#### Outros	

| Expressão Reglar | Descrição                                         | Exemplo                      |
| ---------------- | ------------------------------------------------- | ---------------------------- |
| \|               | Ou. Possibilita mais de um conjunto de caracteres | banana \| uva \| pera        |
| \                | Escape. Protege um meta-caratecere                | 20\\+30                      |
| (...)            | Grupo. Agrupar varios tipos de conjuntos          | (ba){1,3} = ba, baba, bababa |



#### VIM

##### Interações

| Comando        | Descrição                                                    | Exemplo                            |
| -------------- | ------------------------------------------------------------ | ---------------------------------- |
| /{palavra}     | Busca por palavra no texto do começo pro final               |                                    |
| /{palavra} + n | Next word found                                              |                                    |
| /{palavra} + N | Previus word found                                           |                                    |
| ?{palavra}     | Busca por palavra no texto do FINAL pro começo               |                                    |
| H + J + K + L  | ← ↑ ↓ → ( Movimentação no texto)                             |                                    |
| I   ou  U      | Insert - Inicia o modo edição                                |                                    |
| c+c            | Cut, entrando em modo edição                                 |                                    |
| p              | Paste                                                        |                                    |
| d{numero}+d    | Cut, sem entrar em modo edição                               | d10d - Corta as proximas 10 linhas |
| y+y            | Copiar                                                       |                                    |
| :q             | Sair sem salvar                                              |                                    |
| :w             | Salvar                                                       |                                    |
| :e             | Edit - Carrega novamente o arquivo aberto (caso tenha sido alterado em background) |                                    |



#### Selecionar o editor padrão do shell

```BASH
select-editor # Mostra todas as opções e qual está configurada. Pergunta qual a nova configuração

export EDITOR=nano # Exporta a variavel de ambiente que define o editor padrão
```

### 104.1 Criando Particções e Sistemas de Arquivos (2)

#### Comandos Uteis

```bash
fdisk # Manipulador das tabelas de partição. Com ele é possível Criar, remov, Alterar
	  # Caracteristicas
	  #		* Espaço, tipo (Swap, Linux), tamanho, formato (DOS, GPT)
	# Principais comandos
	m # MENU
	p # Print
	n # New
	w # Write
	t # Change partition TYPE
	  
gdisk # Fdisk para partições GPT
```



#### Tipos de File Systems

* EXT2
* EXT3 = (EXT2 + jounarctl)
* EXT4 = (EXT3 + Melhorias  de espaço e velocidade)

##### Comandos

```bash
mkfs # Cria um Linux file system 
	-t ext3 /dev/sdb2
	
mkswap # Prepara uma area de Swap

parted # Alterar o inicio e fim de partições (tamanho)
gparted # Grafico do parted (UI)
```



#### Criação de Swap

```bash
fdisk /dev/sdb
	'->	n # Criar uma nova partição: Ponto de começo default: Tamanho desejado: 
	'-> t # Alterar tipo da partição: Hex Code = 82
    '-> w # Write (salvar)
# Formata o arquivo 
sudo mkswap /dev/sdb2    

# Ajusta permissao 
chmod 0600 /dev/sdb2     

# Ativa o arquivo como área de swap`
swapon /dev/sdb2 
```



##### BTRFS

Novo file System para Linux. Possivel substitudo do EXT4.

Realiza compactação dos arquivos em tempo de execução



##### exFAT - Proprietário Microsoft

Extended File System - Usado em pendrives e cartões de memoria

Intermediario entre FAT e NTFS

Proprietario Microsoft, assim como o FAT e NTFS. O linux pode usar com a instalação dos pacotes

* exfat-fuse
* exfat-utils

### 104.2 Mantendo a Integridade de Sistemas de Arquivos (2)

#### Comandos

```bash
df # disk file system usage
	-i # INodes usage
	-h # Human

	

du # disk usage
	-s # Sumaraze
	-h # Human Readable
	--max-depth=2 # Apenas para 2 dir de profundidade
	-a # All
	/dir # Busca apenas no diretório
	
fsck # File System check
e2fsck # EXT* File System Check

tune2fs # Altera propriedades de config gerais dos files systems



dumpe2fs # Dump do file system. Mostra todas as infos



xfs_repair # Repara XFS 
xfs_fsr	   # File System Reorganizer XFS
xfs_db	   # Debug XFS
```



### 104.3 Controle de Montagem e Desmontagem de FSs (3)

#### Comandos Uteis

```bash
mount 
	-a
	--bind # Entre diretórios 
		   # mount --bind /opt /mnt/montagem
umount
	-a
	
/etc/fstab # 
	#	Partição		Diretório		FS		  Opções	  Dump (Backup)		Checagem FS (fsck)
    #  /dev/sdb1	/tmp/mount_test		auto	defaults		0					0	
    #  /dev/sdb2	none				swap	defaults		0					0
    
lsblk # Mostra todos os blocos de dispositivos (partições)

blkid # Mostra as informações das partições
```

Montagem de Swap não funciona pelo -a. porém se estiver correto funcionaŕa ao ligar a maquina

#### Principais opções utilizadas nas configurações do /etc/fstab

- rw = Partição aceita gravação de dados (ReadWrite)
- ro = Partição não aceita gravação de dados, é apenas leitura (ReadOnly)
- auto = Partição será montada automaticamente durante o boot e quando usado o comando "mount -a)
- noauto = Proíbe a montagem automática. Normalmente utilizado com dispositivos removíveis.
- sync = Estabelece E/S síncrona
- async = Estabelece E/S assíncrona
- dev = Interpreta dispositivos especiais, como os existentes na partição /dev
- exec = Habilita a execução de programas contidos nessa partição
- noexec = Proíbe a execução de programas executáveis contidos na partição
- suid = Habilita o uso do SUID e SGID
- nosuid = Desabilita o uso do SUID e SGID
- user = Permite que um usuário comum monte um sistemas de arquivos, mas apenas este usuário conseguirá desmontá-lo
- users = Permite que um usuário comum monte um FS, mas qualquer usuário poderá desmontá-lo
- nouser = Apenas root pode montar e desmontar a partição
- usrquota = Habilita o uso de quotas por usuário
- grpquota = Habilita o uso de quotas por grupo
- defaults = Define o conjunto de opções: rw, suid, dev, exec, auto, nouser e async



#### Ponto de montagem por SystemD

```bash
No diretório /etc/systemd/system
Criar um arquivo {diretório-montagem}.mount # Exemplo /mnt/montagem = mnt-montagem.mount
Com o conteudo

###
[Unit]
Description=Ponto de Montagem por SystemD

[Mount]
What=/dev/sdb3
Where=/mnt/montagem
Type=ext4
Options=defaults

[Install]
WantedBy=multi-user.target
###

Rodar com o comando

systemctl start mnt-montagem.mount
```





#### Formas de listar as montagems

```bash
df 
lsblk
cat /proc/self/mounts
cat /proc/mounts
systemctl list-units --type=mounts
```

### 104.5 Gerenciando Permissões e Propriedades de Arquivos (3)

#### Permissão especial Stick Bit

Permissão para diretórios onde apenas o dono do diretório e quem criou o arquivo tem permissão de remover.

Para adicionar a permissão Stick Bit existem as seguinte maneiras:

```
# Para Adicionar
chmod 1777 dirTeste # 1 é repsonsavel por ativar o Stick Bit
chmod o+t dirTeste # t = s{t}ick bit

# Para remover
chmod 0777 dirTeste
chmod o-t dirTeste
```



#### UMask

Mascara para criação default de arquivos e diretórios

Mascara: 	0002

Arquivo: 	 6666

Diretório:    7777

Permissão são o resultado da subtração;



### 104.7 Encontrando Arquivos de Sistemas e sua Localização Correta (2)

#### Comandos Uteis

```bash
find
	-print0  # Transforma a quebra de linha da saida ($) em null (@). Facilita para utilizar com outros comandos
			 # find ./ -print0 | xargs -0 ls -l

locate

whereis

which

type
```



### Diretórios para memorizar

```bash
SysV
	# /etc/initab
	# /etc/init.d/
	
SystemD
	# /etc/systemd/system
	# /usr/lib/systemd/system
	# /lib/systemd/system/default.target
	
Kernel
	# /var/log/messages
	# /var/log/dmesg

Dispositivos de Hardware
	# /proc/ioports
    # /proc/interrupts
    
Repositórios
	YUM
	# /etc/yum.repos.d/
	# /etc/yum.conf
	
	APT
	# /etc/apt/source.list
	

Libraries
	# /etc/ld.so.conf
	# /etc/ld.so.conf.d/
	# /etc/ld.so.cache
		
```

