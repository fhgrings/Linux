# Resumo Estudos Linux LPIC-1

Autor do resumo: Felipe Grings

Data de inicio: 14/12/2020

Material de estudo:

*  [Preparatório para Certificação Linux LPIC-1 | Atualizado V5](https://www.udemy.com/course/curso-online-certificacao-linux-lpic1-comptia/) 
* 

Plataforma: Udemy



Objetivo: Resumo para estudos e compreensão das matérias exigidas no exame de certificação LPIC 101/2-500

# PROVA 101-500

## Tópico 101: Arquitetura do Sistema

### 101.1 Identificar e Configurar os Dispositivos de Hardware (2)

#### Bios - Basic Input Output System

​	Firmware da placa mão, responsavel pelo boot

#### EFI - Extensible Firmware Interface

​	Segunda geração da BIOS, responsavel por diferesas outras funcionalidades no boot do sistema

#### UEFI - Unfied EFI

----



#### IRQ's - Interrupt Requests

```bash
cat /proc/interrupts
```

​	

| IRQ1  | Teclado              |
| ----- | -------------------- |
| IRQ3  | Porta Serial 2 (VGA) |
| IRQ4  | Porta Serial 2 (VGA) |
| IRQ14 | IDE Primaria         |
| IRQ15 | IDE Secundária       |



---

#### Endereços de I/O

Lista de Endereços de memoria fixos para cada periférico

```bash
cat /proc/ioports 
0000-0000 : PCI Bus 0000:00
0000-0000 : dma1
0000-0000 : pic1
0000-0000 : timer0
0000-0000 : timer1
0000-0000 : keyboard
0000-0000 : keyboard
0000-0000 : rtc0
0000-0000 : dma page reg
0000-0000 : pic2
0000-0000 : dma2
0000-0000 : fpu

```

#### DMA - Direct Message Access

Acesso direto ao dispositivo, sem a necessidade de interupção ao processador

#### Conexoes de Hardware 

PCI - lspci

USB - lsusb



/dev - devices -> udev

/sys - Hardware - sysfs

/proc - processos  -> Arquivos lidos pelo kernel

/proc/cmdline -> Parametros de boot para inicialização



#### Systemctl-udev

```bash
udevadm monitor # Traz todas as informações em tempo real dos periféricos (USB, Power Suply)
```



**1.** Verifique as seguintes informações referentes aos dispositivos de hardware de sua máquina:

- Nome do dispositivo de áudio/multimídia que está sendo utilizado

```bash
lspci
```

- O IRQ utilizado pelo dispositivo de áudio/multimídia

```bash
lspci -v -s <ID>
cat /proc/interrupts
```

- Quantidade de devices conectados em seu barramento USB

```bash
lsusb | wc -l
```

- Nome do modelo da(s) CPU(s) utilizadas

```bash
cat /proc/cpuinfo
```

- Como o dispositivo de armazenamento foi mapeado em seu Linux (sda, sdb, hda, hdb)

```bash
dmesg
df
fdisk -l
```



**2.** Quais as dependências do módulo "snd"

```bash
modinfo snd
lsmod
```

**3.** Carregue o módulo batman-adv em seu sistema Linux, verifique se o mesmo foi corretamente carregado e em seguida descarregue-o.

```bash
modprobe batman-adv /lib/modules/4.8.0-46-generic/kernel/net/batman-adv/batman-adv.ko
#insmod

lsmod | grep batman
rmmod barman-adc 
#modprobe -r batman-adv
```

**4.** Quais as vantagens do uso do *modprobe* no lugar do *insmod* e *rmmod*?

```bash
O modprobe possui o mapeamento de todos os nomes dos módulos, seu arquivo .ko e
suas dependências, com isso ele consegue subir ou baixar um módulo apenas por sue
nome, não sendo necessário a indicação do caminho completo. Consegue também
subir e baixar um módulo e suas dependências.
```



### 101.2 O Boot do Sistema (3)

#### Fluxo de Boot 

##### Utilizando a BIOS

```bash
#  BIOS 	----> 	MBR		----->  	BootLoader  	-----> 	Kernel 	---->	Init
```

##### Utilizando a UEFI

```bash
#  BIOS 	---->  	BootLoader  	-----> 	Kernel 	---->	Init
```

UEFI não utiliza o **MBR**

Obtem os dados pelo ESP (EFI System Partition)

* ​	Montada no diretório /boot/efi/
* Utiliza um filesystem do tipo **FAT**

Utiliza (preferencialmente) partições GPT ao inves de MBR

* Usport partições alem do limite de 2TB (GPT)

Implementa o Boot Seguro (Só faz o boot atraves de imagens assinadas)

##### Comandos

```bash
efibootmgr
```





BIOS -> Localiza e executa o MBR

MBR (Master Boot Record) -> Executa o Bootloader

Bootloader -> Selecionar e executa o Kernel e o initrd -- GRUB (Grand Unified Boot) / LILO (Original antigo)

Kernel -> Executa o /sbin/init

Init -> Inicia os programas do runlevel/target definidos



#### Arquivos  de log do boot e kernel

```bash
dmesg
cat /var/log/messages/
cat /var/log/dmesg


##

jouurnalctl -b
jouurnalctl -k
```





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





#### **Exercicios**

**6.** Identifique se a sua instalação Linux está utilizando SysV, systemd ou upstart.

```bash
ps -p 1
stat /proc/1/exe
```



**7.** Identifique o runlevel ou target padrão em seu sistema de inicialização SysV ou systemd.

```bash
ls -lh /lib/systemd/system/default.target
systemctl get-default
```



**8.** Em um sistema systemd, identifique a quantidade de targets existentes.

```bash
ls -l /lib/systemd/system/*target | wc -l
systemctl list-units --type=target
```



**9.** Em um sistema com systemd, verifique se o serviço cron está sendo  iniciado por padrão no target default. Se não estiver, faça com que  seja, se estiver, desabilite.

```bash
systemctl is-enabled cron
# Para ver se o serviço está em execução
systemctl status cron
# Para habilitar ou desabilitar o serviço
systemctl enable/disable cron
```



**10.** Informe pelo menos 3 comandos diferentes para reiniciar uma máquina Linux.

```bash
shutdown -r
reboot
init 6
telinit 6
systemctl reboot
```



## Tópico 102: Instalação do Linux e Gerenciamento de Pacotes

### 102.1 Design do Layout do HD (2)

#### Conceitos 

##### Partição

* Area "fisica" delimitada dentro do disco

Vantagens

* Gerenciemto do espaço em Disco
* Diferetens tipos de FileSystem para cada partição
* Proteção contra erros do Disco
* Backup facilitado



##### Sistema de Particionamento

##### MBR: Master Boot Record (Mencionado nos estudos de boot) - FOCO LPIC

* Padrão mas é limitado a 2TB por partição

TIpos de partição

* Primária
* Extendida ( Tipo de primária): Contem as partições logicas
* Lógica
  * Limita a 4 partições primarias ou 3 primarias e 1 extendida
  * Primarias numeradas de 1 a 4. Ex.: sda1, sda3, sda4
  * Logicas numeradas a partir de 5. Ex.: sda5, sda6

| Primária | Extendida         | Primária | Primária |
| -------- | ----------------- | -------- | -------- |
| Fisica   | (Logica) (Lógica) | Fisica   | Fisica   |



##### GPT: GUID Partition Table 

* Utilizado quando são necessárias partições maiores de 2 TB
* maioria daos sistemas com EFI utilizam GPT



Primeira partição criada "/"

Deve existir ao menos 2 partições "/" e "swap"



Diretórios que NÂO podem estar fora do "/"

* /etc
* /bin
* /sbin
* /dev
* /proc
* /sys

##### Ponto de montagem

* Configuração simbolica para o linux atribuir a partição a uma area especifica do File System





##### LVM - Logical Volume Management

* Metodo para alocar espaço dos discos em volumes logicos
* Facilita o redimensionamento
* Elementos
  * VG: Volume Group
  * PV: Physical Volume
  * LV: Logical Volume
  * PE: Physical Extent
  * LE: Logical Extent



##### UEFI e ESP

UEFI - Boot aprimorado 

```bash
df -T # Mostra o boot utilizado "/boot/efi"


efibootmgr # Manipulador do UEFI Boot Manager

```





### 102.2 Instalação do Boot Manager (2)

#### GRUB - É o multicarregador de sistema operacional ( Tela inicial do F12 )

|                          | GRUB LEGACY                                                  | GRUB2                                                        |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Arquivos de configuração | **/boot/grub/menu.lst**<br />/boot/grub/grub.cong/cnf        | **/boot/grub/grub.cgf**<br />/etc/default/grub<br />/etc/grub.d |
| Referencia ao disco      | hda1 = **hd0,0**<br />hda5 = hda0,4<br />hd3 = hda1,2        | hda1 = **hd0,1 ou hd0,msdos1**<br />hda5 = hda0,5<br />hd3 = hda1,3 |
| Comandos                 | grub-install /dev/sda                                        | grub-install <device><br />update-grub<br />grub-mkconfig -o<br />/boot/grub/grub.cfg |
| Principais Parametros    | title "Ubuntu"<br />**root(hd0,0)<br />kernel /boot/vmlinuz-4-8.20-93<br />**generic ro root=/dev/sda5 | menuentry "Ubuntu" **{<br />set root=(hd0,1)<br />linux** /boot/mvlinux-4-5.182<br />} |
|                          |                                                              |                                                              |



### 102.3 Gerenciamento de Bibliotecas Compartilhadas (1)



##### Sistema padrão

Cada aplicação carrega sua propria biblioteca.

Problemas - Aumento do tamanho das aplicações e necessidade de recompilar biblioteca por biblioteca caso haja alteração

---

Aplicação A			Aplicação B

   	/\							   /\

Biblioteca 1			Biblioteca 1

---

##### Biblioteca Comparilhada

Aplicação A			Aplicação B

​						\/

​				Biblioteca 1



##### Comandos

```bash
ldd <caminho da aplicação> # Apresenta todas as libs

ld # Realiza a linkagem entre as libs e o cache de busca do linux

```



##### Adicionar uma lib 

```bash
vi /etc/ld.so.conf
	# Adicionar o caminho do diretório Ex.: /tmp/libs

# Para validar
ldconfig -p | grep "/tmp/libs"

# Adicionar lib temporaria - APENAS PARA A SESSÂO
EXPORT LD_LIBRARY_PATH=/tmp/libs
```



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



#### Exercicios

**17.** Faça o download do pacote rpm do editor de textos "nano"

```bash
yum install -y nano 
yum install --downloadonly --downloaddir=/tmp nano
```



**18.** Utilizando o comando rpm, instale o "nano"

```bash
rpm -i nano.rpm
rpm -ivh nanoXXXXXXX.rpm
```



**19.** Consulte a versão do nano que foi instalada

```bash
rpm -qi nano
```



**20.** Utilizando o yum, remova o pacote nano

```bash
yum erase nano
```



**21.** Utilizando o yum, faça o upgrade de todos os pacotes de sua distribuição RedHat-based, sem remover pacotes obsoletos.

```bash
yum update
```



**22.** Encontre a aplicação relacionada ao arquivo /etc/sudoers

```bash
rpm -qf /etc/sudoers
```



### 102.6 Linux e Virtualização (1)



## Tópico 103: Comandos GNU e Unix (40% da prova 101)

### 103.1 Trabalhando com linha de comando (4)

##### Shell - Interface usuário/OS

* **bash** - Cobrado pela LPI
* sh
* csh
* ksh

##### Descobrir o Shell que está sendo rodado

`echo $SHELL`

##### Descobrir se o comando é um comando interno ou externo

`type {comando}`

##### Redirecionamento de comando pela variavel PATH



#### Variaveis

```bash
set | less #-> saida para toads as variaveis do sistema junto com as exportadas
env | less #-> todas as variaveis do bash
unset VARIAVEL #-> Remove a variavel
$$ #-> PID do processo atual
$! #-> PID do utlimo processo executado em background
$? #-> Exit code do ultimo processo
```



#### Execução de comandos sequenciais

```bash
date ; ls /tmp ; pwd # ; executa de forma sequencial independente de erro na saida anterior
date && ls /tmp && pwd #  && executa apenas se a saida anterior for 0
date || ls /tmp || pwd # || executa apenas se a saida do anterior der erro
```



#### History - unico por usuário

```bash
history
!! # Executa o ultimo comando executado
!13 # Executa o comnado da linha 13
!uname # Executa o ultimo comando que contem a string "uname"
history -c # Limpa o arquivo history do usuário

set | grep HISTFILE # Mostra a variavel que armazena o local do arquivo history
ctrl+R -> Busca os comandos no history

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



#### Quoting

Proteção dos caracteres especiais na execução dos comandos

```bash
\     # Ignora o proximo caractere especial
" "   # Ignora caractere especial exceto     $ ` \
' '   # Ignora todos os caracteres especiais

echo *         # Printa tudo da pasta
echo "*"       # Printa *
echo "$TESTE"  # Printa o valor de TESTE
echo '$TESTE'  # Printa $TESTE

```



#### Exercicios 

**1.** Encontre as seguintes informações sobre a sua instalação Linux:

- O caminho completo do arquivo .bash_history para o seu usuário;

  - ```bash
    echo $HISTFILE
    ```

- O release do kernel instalado

  ```bash
  uname -r
  ```

- Os diretórios incluídos em seu PATH

  ```bash
  echo $PATH
  ```

- O hostname da máquina

  ```bash
  hostname
  ```

- O PID da sua sessão shell atual

  - `echo $$`

- A localização do comando tar

  - `which tar`

**2.** Crie e exporte uma variável chamada "NOME" que contenha o seu nome completo.

* `export NOME="Felipe Hauschild Grings"`

**3.** Crie um comando que escreva na tela a seguinte frase: "O Conteúdo da Variável $NOME é: <Valor da Variável NOME>"

* ```bash
  echo "O Conteudo da Variavel \$NOME é: $NOME"
  ```

  

### 103.2 Aplicando Filtros a Textos e Arquivos (2)

#### Comando importantes

```bash
cat # Printa o arquivo
tac # Inverso de cat. Imprime o arquivo do fim pro inicio
head # Printa o cabeçalho 
head -2 # Primerias 2 linhas
head -c40 # Primeiros 40 caracteres
tail # Printa o fim

less # Paginação da saida
	 # Barraca de espaço passa a pagina
	 # /Palavra -> Busca a palabra
	 	# "n" -> Avança para a proxima palavra encontrada
	 	# Shift+N -> Volta a palavra
 
wc # Conta quantidade de linhas/palavras/caracteres do arquivo
   # -l -> apenas linhas
   # -w -> apenas palavras
   # -c -> apenas caracteres

nl # Numera as linhas do arquivo

sort {Arquivo} # Ordenar a saida do arquivo
	 		   # -d  -> Ordem reversa
	 		   # -k2 -> Ordem por segundo campo

####

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



#### Exercícios

**4.** O arquivo /etc/passwd contém a lista de usuários  do Linux, os campos são separados pelo caractere :, o primeiro campo  indica o nome do usuário e o terceiro o ID do usuário.

Escreva um comando que mostre os últimos 15 registros do arquivo, exibindo apenas o nome do usuário e seu ID, e que esteja ordenado pelo ID numérico. Por  exemplo:

usuario1:10

usuario2:12

usuario:3:1000

.......

R:

```bash
tail -15 /etc/passwd | cut -d ":" -f1,3 | sort -t ":" -k2 -g
```

 

**5.** Gere um comando, ou sequência deles, que mostre o  número de linhas do arquivo /etc/passwd excluindo-se as linhas que  contenham a palavra "daemon". O resultado do comando deve ser o número  de linhas.

R:

 ```bash
sed '/daemon/d' /etc/passwd | wc -l
 ```



### 103.3 Gerenciamento Basico de Arquivos (4)

#### Comandos importantes 

```bash
cd  # Change Directory
	# .  -> Diretório atual
	# .. -> Diretório pai
	# -  -> Diretório anterior
	# ~  -> Diretório Home do usuário atual
	
ls  # List Directory
	# -a -> All
	# -h -> Human Readable
	# -R -> Recursivo
	# Arquivo{Expressões regulares} -> ?,*,[123],[!123],{ula,ULA}

file # Analise do arquivo

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



#### 

#### Exercícios

**6.** No home de seu usuário, crie um diretório chamado LPI1, dentro dele crie Aulas, Exercicios e Exemplos.

```bash
mkdir -p LPI1/{Aulas,Exercicios,Exemplos}
```



**7.** Copie (não mova) todos os arquivos e diretórios existentes em /etc/network/  para <HOME>/LPI1/Exercicios/Network/. Mantenha as mesmas  permissões.

```bash
cp -rp /etc/network/* ~/LPI1/Exercicios/Network/
```



**8.** Copie (não mova) todos os arquivos do diretório /etc, cujo nome termine com ".conf" para <HOME>/LPI1/Exercicios/Config/ 

```bash
cp -r /etc/*.conf ~/LPI1/Exercicios/Config
```



**9.** Em <HOME>/LPI1/Exercicios, crie um arquivo chamado  arquivos-cron.tgz, compactado com o gzip, contendo todos os arquivos e  diretórios do /etc que contenham a palavra "cron" no nome. 

```bash
tar cvzf ~/LPI1/Exericios/arquivos-cron.tgz /etc/*cron*
```



**10.** Descompacte conteúdo do arquivo arquivos-cron.tgz dentro do diretório <HOME>/LPI1/Exercicios/Descompactar/

```bash
tar xvzf arquivos-cron.tgz ~/LPI1/Exercicios/Descompactar
```



**11.** Encontre todos os arquivos do diretório /var, cujo nome termine com ".gz" e que foram modificados nas últimas 48 horas.

```bash
find /var -name *.gz -mtime -2
```





### 103.4 Fluxo, Pipes e Redirecionamentos (4)

#### Redirecionamentos

STDIN (0) --------  PROCESS  ---------> STDOUT (1)

​								     '-----------------> STDERROR (2)

Conteudo | Executor ----- Executor < Conteudo

| Standart | Comando                                                      |
| -------- | ------------------------------------------------------------ |
| STDIN    | Conteudo \| Executor <br />Executor < Conteudo<br />Executor <<< teste.txt    (Considera como string e não arquivo)<br /> Executor << {String} Texto Qualquer {String} (Indicador de End Of File) |
| STDOUT   | 1>           1>>         >        >>                         |
| STDERROR | 2>           2>>                                             |



```bash
tee # Printar o resultado na tela e redirecionar a saída para um arquivo
sort {Arquivo} | uniq | tee resultado.txt


xargs # Redireciona a saida do ultimo como entrada do a ser executado
ls | xargs rm -v
find /home -name "Teste*" | xargs rm -v
```



#### Exercícios

**12.** Gere um comando que crie um arquivo chamado  diretorios-config.out, contendo a saída do comando "ls" (usando as  devidas opções) para todos os diretórios do /var cujo nome contenha a  palavra "config". A saída deve ser algo como o visto abaixo:

drwxr-xr-x 2 root  root  4096 Mar 28 11:49 /var/cache/fontconfig 

drwx------ 2 root  root  4096 Abr 7 11:37 /var/cache/ldconfig 

drwxr-xr-x 2 lightdm lightdm 4096 Mar 27 16:41 /var/lib/lightdm/.cache/fontconfig 

drwx------ 4 lightdm lightdm 4096 Mar 27 16:41 /var/lib/lightdm/.config

R:

```bash
find /var -name "*config*" | xargs ls -l > diretorios-config.out
```



**13.** Explique as diferenças entre os seguintes redirecionadores de entradas e saídas

- \> arquivo    : Sobreescreve o arquivo com STDOUT

- < arquivo    : Enviar como STDIN para o comando anterior

- \>> arquivo : Realiza o apend no arquivo com STDOUT  

- 2> arquivo : Sobreescreve o arquivo com STDERR

- \>arquivo 2>&1  : Sobreescreve o arquivo com STDOUT e STDERR

  

**14.** Escreva um único comando comando que gere a lista de arquivos e diretórios  contidos em ~/LPI1/Exercicios/Network, exibindo-os na tela e em um novo arquivo chamado lista-network.out

R:

```bash
ls -l ~/LPI1/Exercicios/Network | tee lista-network.out
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



#### Exercícios

**15.** Preencha as informações abaixo com os dados de sua instância Linux:

- Total de Memória RAM utilizada (em MB):

```bash
free -m
```



- Load Average (Média dos Últimos 5 minutos): 

```bash
top
```



- Quantidade de Processos em Execução: 

```bash
ps aux | wc -l
```



- PID dos 3 processos que estão utilizando mais Memória:

  ​	Pelo top, ordenar com a opção M. Obter os 3 primeiros PIDs

- PPID (Parent Process ID) dos 3 processos com maior tempo de Uso de CPU: 

  ​	Pelo top, ordenar com a opção T. Ainda no top, pressionar f para
  ​	adicionar novos campos, e selecionar o campo PPID. Obter os 3
  ​	primeiros PPIDs.

  ​	Ou obter os PIDs pelo top e buscar o PPID pelo comando “ps -la |grep
  ​	PID”



**16.** Crie um comando, que gere um arquivo chamado  ~/LPI1/Exercicios/resultado-top.out, que contenha a saída do comando  top, atualizado a cada 10 segundos, sendo executado indefinidamente até  que o processo seja morto. O comando deve rodar em background.

```bash
top –b –d10 > ~/LPI1/Exercicios/resultado-top.out &
```



**17.** Envie um sinal de SIGKILL para o processo iniciado no exercício 16.

```bash
ps axu |grep "top -b d10"
kill -9 <PID>
```





### 103.6 Modificar a Prioridade de Execução de Processos(2)

| Propriedade | Descrição                                                    |
| ----------- | ------------------------------------------------------------ |
| PR          | Propriedade interna de administração de prioridades do Linux |
| NI          | Nice - Propriedade ajustavel pelo usuário                    |

#### NI - Regras de uso

* Os valores variam de -20 à +19. Onde -20 é prioridade máxima
* Se nada for feito, começa com valor 0
* Usuário padrão pode alterar a prioridade de seus processos entre 0 e +19
* Apenas o usuário root pode alterar abaixo de 0
* Processo iniciado pelo nice, inicia em NI=10



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



#### Exercícios

**18.** Inicie o mesmo comando aplicado no exercício 16, porém com a menor prioridade possível.

```bash
nice -n -19 top –b –d10 > ~/LPI1/Exercicios/resultado-top.out &
```



**19.** Altere o NICE do processo "rsyslogd" para o valor -10.

```bash
renice -n -10 $(pgrep rsyslogd)
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





#### Exercícios

**20.** Gere um comando que exiba na tela todas as linhas do arquivo /etc/passwd que terminem com "nologin" 

```bash
egrep "nologin$" /etc/passwd
```



**21.** Crie um comando que liste todos os arquivos do diretório /etc/ que contenham a palavra "eth0" em seu conteúdo, não no nome do arquivo. A pesquisa  deve incluir também os subdiretórios. Apenas o nome e caminho do  arquivo deve ser exibido.

```bash
egrep -rl "eth0" /etc/*
```



**22.** No arquivo  /etc/passwd, o primeiro campo indica o nome do usuário, enquanto que o  terceiro indica o ID do usuário. Crie um comando que exiba apenas os  nomes de usuários que tenham o ID com 3 dígitos.

```bash
egrep "[a-zA-Z]:[0-9][0-9][0-9]:" /etc/passwd | cut -d":" -f1
```



**23.** Com base no arquivo alunos.txt, crie um novo arquivo chamado  alunos-exercicio.txt contendo o mesmo conteúdo do arquivo alunos.txt mas fazendo com que toda ocorrência a "Ana Maria" seja substituído por  "Marieta".

```bash
sed -e "s/Ana Maria/Marieta/g" alunos.txt  > alunos-exercicio.txt
```



### 103.8 Edição Basica de Arquivos (3)

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



#### EMACS

Roda em interface grafica e shell

##### Interações

| Comando | Descrição                                 | Exemplo |
| ------- | ----------------------------------------- | ------- |
| ctrl+k  | Copiar (Guarda em buffer todos os copies) |         |
| ctrl+y  | Colar                                     |         |
|         |                                           |         |



#### Selecionar o editor padrão do shell

```BASH
select-editor # Mostra todas as opções e qual está configurada. Pergunta qual a nova configuração

export EDITOR=nano # Exporta a variavel de ambiente que define o editor padrão
```



#### Exercicios

**24.** Crie um arquivo texto chamado meu-curriculo.txt. Nele inclua as seguintes seções:

- Dados Pessoais: Contendo nome, idade, endereço e telefone
- Experiência Profissional: Nome das 3 últimas empresas em que trabalhou
- Cursos Realizados: Nome dos últimos 3 cursos que realizou, pode incluir este.
- Certificações Obtidas: Todas as suas certificações. Já pode incluir a LPIC-1 como uma delas ;)
- Seu Objetivo de Carreira para os Próximos 5 anos.

Salve e saia do arquivo.

```bash
vim meu-curriculo.txt
	Dados Pessoais:
    Felipe
    22
    Esteio
    1231
    Exp Prof:
    ilegra
    UNISINOS
    PROCERGS
    Cursos Reliazdos:
    Shell
    Linux Essentials
    LPI
    Objetivo de carreira dos proximos 5 anos:
    Estar trabalhando remoto com o que gosto, de forma profissional, onde serei referencia 
    para a empresa juntamente com outros grandes profissionais da area. Trabalhar poucas horas por dia
    E com flexibilidade de horario para poder viajar e aproveitar a vida.
:wq!

```



**25.** Abra novamente o arquivo meu-curriculo.txt, faça as seguintes modificações:

- Mova a seção de "Objetivo" para que ela fique após os "Dados Pessoais"
- Após o ítem "Experiência Profissional", adicione a seção "Formação  Escolar", incluindo informações de escolaridade (ensino médio, técnico, superior, pós e etc)

Salve e saia do arquivo.

```bash
#Seleciona o cursor na linha Objetivos
d4d
#Seleciona o curso na linha anterior ao Exp Prof:
p

```



## Tópico 104: Dispositivos, Sistemas de Arquivos FHS

### 104.1 Criando Particções e Sistemas de Arquivos (2)

#### Comandos Uteis

```bash
fdisk # Manipulador das tabelas de partição. Com ele é possível Criar, remov, Alterar
	  # Caracteristicas
	  #		* Espaço, tipo (Swap, Linux), tamanho, formato (DOS, GPT)
	# Principais comandos
	m # MENU
	p
	n
	w
	t
	  
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

Modo Crestani

```bash
# Criar arquivo com 5GB
sudo dd if=/dev/zero of=/var/swapfile/swapfile5G bs=1024 count=5242880
# Formata o arquivo 
sudo mkswap /var/swapfile/swapfile5G    
# Ajusta permissao 
chmod 0600 /var/swapfile/swapfile5G     
# Ativa o arquivo como área de swap`
swapon /var/swapfile/swapfile5G        
```



Modo LPI

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

tune2fs # Altera propriedades de config gerais dos files systems

dumpe2fs # Dump do file system. Mostra todas as infos

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

### 104.6 Criando e Alterando Links (2)

#### *IMPORTANTE*

Link Hard não funciona para:

*  Diretórios 
* Arquivos em outras partições; Link hard se baseia em Inodes e cada partição tem sua propria distribuição.



#### Estruturação de dados linux

Os arquivos são alocados em memória localizados Inodes. Cada Inode deve conter 1 conexão para exister.

Link simbolico gera um novo Inode apontando para o Inode do arquivo apontado

Link Fisico apresenta um novo file apontando exatamente para o mesmo Inode

#### 

#### Representação grafica

[![image-20200729191000503](https://github.com/fhgrings/Shell-Playground/raw/master/home/teste/.config/Typora/typora-user-images/image-20200729191000503.png)](https://github.com/fhgrings/Shell-Playground/blob/master/home/teste/.config/Typora/typora-user-images/image-20200729191000503.png)

#### 

#### Código exemplo

```
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

#### 

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







---



# PROVA 102-500

## Tópico 105: Shells e Shell Scripting

### 105.1 Customização e Uso do Ambiente Shell 

##### Execução de scripts utlizando o bash local

```bash
source script.sh # Roda o script localmente, carregando todas as variaveis e funções para o bash
. /scripts.sh 	 # Igual /\
```



##### Declaração de funções no bash Local

```bash
function funcao1 { date; uptime; }

funcao2 () { date; uptime; }
```



##### Aruivos de configuração

```bash
## Default para todos os usuários

/etc/profile # Arquivo de inicialização. Primeiro arquivo executado ao login de todos os users
/etc/bash.bashrc # Arquivo executado para toda nova sessão de shell bash, aplicado a todos os usuários. 

/etc/profile.d/ # Todos os scripts do diretorio são executados no login do user
/etc/inputrc	   # Inputs quando em terminal Ex: Ctrl+T, EDITOR
/etc/skel/ 		# Arquivos default para criação na home para cada novo usuário

## Especifico para cada usuário. Carregado por ordem que achar primeiro

/home/usuario/.bash_profile # Primeiro
/home/usuario/.bash_login	# Segundo
/home/usuario/.profile		# Terceiro

/home/usario/.inputrc	   # Inputs quando em terminal Ex: Ctrl+T, EDITOR
/home/usuario/.bash_logout # Executado no logout
```



**Principais Variáveis de Ambiente**

É importante conhecer a função de algumas variáveis de ambiente existentes no sistema, as principais são:

- DISPLAY: Indica às aplicações gráficas onde as janelas deverão ser exibidas. Será estudado no Tópico 106
- HISTFILE: Arquivo do histórico de comandos
- HISTFILESIZE: Quantidade de linhas/comandos armazenados no arquivo de histórico
- HOME: Indica o diretório do usuário atual
- LANG: Definição do idioma
- LOGNAME e USER: Nome do usuário atual
- PATH: Diretórios em que o Linux irá procurar por arquivos executáveis
- PS1: Aparência do prompt do shell.
- PWD: Diretório atual
- OLDPWD: Diretório anterior



#### Exercicios

1. Declare de maneira definitiva, para todos os usuários, uma função  que limpe a tela e gere o resultado abaixo ao se digitar "inicio".

   *"Seja bem-vindo <usuário> Este sistema está ativo desde <1999-01-01 11:11:11>*

   *Bons Estudos!"*

   \* <> indica o uso do resultado de uma variável ou comando

```bash
# Inserir no arquivo /etc/profile a seguinte função
function inicio {
    clear
    echo "Seja bem-vindo $USER"
    echo "Este sistema está ativo desde `uptime -s`"
    echo ""
    echo "Bons Estudos!"
}
```

**2.** Configure apenas o seu usuário para executar a função "inicio" em todo novo login.

```bash
No arquivo ~/.profile (ou ~/.bash_profile ou ~/.bash_login se existirem), insira na
última linha a chamada para a função inicio.
```

**3.** Faça que a mesma configuração realizada no passo 2 esteja presente para todos os novos usuários criados a partir de agora.

```bash
Edite o arquivo /etc/skel/.profile, e insira na última linha a chamada para a função
inicio.
```

**4.** Muitos usuários andam apagando arquivos indevidamente e depois solicitando que você recupere backups. Para reduzir o problema, você resolve  configurar o ambiente para que o comando "rm" sempre peça uma  confirmação antes de efetivamente apagar o arquivo ou diretório. Faça a  configuração para todos os usuários.

```bash
alias rm=”rm –i”
```





### 105.2 Customização e Criação de Scripts Simples

Variavel PATH para execução de scripts

##### Parametros

```bash
$# # Quantidade de parametros
$1, $2 # Parametros 1 e 2
```

  

#### Interação

```bash
read NUM # Espera o usuário digitar o valor para armazenar na variavel NUM
```



#### Condições

```bash
VAR=""
VAR2=""

if [[ "$VAR" = "$VAR2" ]]; then
  echo "São iguais if [[]];"
fi

if [[ "$VAR" = "$VAR2" ]]
then
  echo "São iguais if [[]]"
fi

if test "$VAR" = "$VAR2"
then
  echo "São iguais if test"
fi

if [ "$VAR" = "$VAR2" ]
then
  echo "São iguais if []"
fi

[ "$VAR" = "$VAR2" ] && echo "São iguais [] &&"

VAR2="Diferente"
echo "VAR2 = Diferente"

[ "$VAR" = "$VAR2" ] || echo "São diferentes [] ||"

echo "Tarefa 1"
echo "Passagem de parametro ./script.sh {numero}"
[ "$1" -gt 10 ] && echo "$$ $0"
##


case $NUM1 in 
	0)
		echo "Igual 0"
	;;
	1|2|3|4)
		echo "O valor digitado foi entre 1 e 4"
		sleep 3
    ;;
    *)
    	echo "O Valor digitado foi maior que 4"
esac
		
```

#### Loop

```bash

echo "====== For 1"
for (( i = 0; i < 10; i++ )); do
  echo $i
done

echo "====== For 2 (seq)"
for i in $(seq 10)
do
  echo $i
done


echo "====== For 3 (array)"
Frutas=(
"Laranja"
"Ameixa"
"Abacaxi"
"Melancia"
"Jabuticaba"
)

for i in "${Frutas[@]}"; do
  echo "$i"
done

echo "===== While"
contador=0
while [[ $contador -lt ${#Frutas[@]} ]]; do
  echo $contador ${Frutas[$contador]}
  contador=$(($contador+1))
done

echo "Tarefas 2 - laços "
for i in $(seq 0 10)
do
  [ $(($i % 2)) -eq 0 ] && echo "Número $i é divisível por 2"
done

until [ $VAR1 = 0 ]
do
	echo "O valor atual do \$VAR1 é: $VAR1"
	VAR1=`expr $VAR1 + 1`
	sleep1
done
```

#### Comando Test

| Comparação de Strings | Descrição                                 |
| --------------------- | ----------------------------------------- |
| Str1 = Str2           | Retorna true se as Strings são iguais     |
| Str1 != Str2          | Retorna true se as Strings não são iguais |
| -n Str1               | Retorna true se a String não é null       |
| -z Str1               | Retorna true se a String é null           |

| Comparação Numérica | Descrição                                                    |
| ------------------- | ------------------------------------------------------------ |
| expr1 -eq expr2     | Retorna true se os valores são iguais                        |
| expr1 -ne expr2     | Retorna true se os valores não são iguais                    |
| expr1 -gt expr2     | Retorna true se o expr1 é maior que o expr2                  |
| expr1 -ge expr2     | Retorna true se o expr1 é maior ou igual ao expr2            |
| expr1 -lt expr2     | Retorna true se o expr1 é menor que o expr2                  |
| expr1 -le expr2     | Retorna trues se o expr1 é menor ou igual ao expr2           |
| ! expr1             | Nega o resultado da expressão (se for true vira false e vice-versa) |

| Condicionais de arquivos | Descrição                                                    |
| ------------------------ | ------------------------------------------------------------ |
| -d file                  | Retorna se for um diretório                                  |
| -e file                  | Retorna true se o arquivo existir                            |
| -f file                  | Retorna true se o arquivo existir (-f é mais usado porque é mais portável) |
| -g file                  | Retorna true se o GID estiver habilitado no arquivo          |
| -r file                  | Retorna true se o arquivo tiver permissão de leitura         |
| -s file                  | Retorna true se o arquivo tiver um tamanho diferente de zero |
| -u                       | Retorna true se o UID estiver habilitado no arquivo          |
| -w                       | Retorna true se o arquivo tiver permissão de escrita         |
| -x                       | Reteorna true se o arquivo tiver permissão de execução       |







## Tópico 106: Interfaces de Usuários e Desktops

### 106.1 Instalar e Configurar o X11 (2)

#### Instalar o arquivo xorg.conf

```bash
# Com o server X11 parado, rode o comando
Xorg -configure # O arquivo será gerado em /root/xorg.conf.new
```



Como vimos, o principal arquivo de configuração do X11 é o  /etc/X11/xorg.conf. Esse arquivo pode ser utilizado principalmente no  caso de configurações específicas necessárias para algum dispositivo  que esteja utilizando.

O xorg.conf é dividido em seções, como descrito abaixo:

| Module                                                       | Files                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carregamento dinM-CM-"mico de mM-CM-3dulos.<br/>Exemplo:<br/>*Section Module<br/>    Load glx<br/>     Load dbe<br/>     Load extmod  <br/>EndSection<br/>* | Caminhos para alguns arquivos e diretórios utilizados pelo servidor X, como módulos mas principalmente as **fontes**.<br/><br/>Exemplos:<br/><br/>*Section "Files"<br/>    ModulePath  "/usr/lib/xorg/modules"<br/>    FontPath   "/usr/share/fonts/X11/misc"<br/>    FontPath   "/usr/share/fonts/X11/Type1"<br/>    FontPath   "/usr/share/fonts/X11/100dpi"<br/>    FontPath   "/usr/share/fonts/X11/75dpi"<br/>    FontPath   "built-ins"<br/>    FontPath   "unix:/7100"<br/>    FontPath   "tcp/fonts.server.com:7100"<br/>EndSection* |



| **InputDevice**                                              | Device                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Contêm configurações referentes aos dispositivos de entrada, principalmente **mouse** e **teclado**. *Identifier* e *Driver* são parâmetros obrigatórios utilizados para especificar o dispositivo. Além disso parâmetros *Option* podem ser adicionados para implementar configurações específicas<br/><br/>Exemplo:<br/><br/>*Section "InputDevice"<br/>    Identifier "Keyboard0"<br/>    Driver   "kbd"<br/>    Option   "XkbModel" "pc105"   <br/>    Option   "XkbLayout" "us"   <br/>    Option   "AutoRepeat" "500 200"<br/>EndSection*<br/><br/>*Section "InputDevice"<br/>    Identifier "Mouse0"<br/>    Driver   "mouse"<br/>    Option   "Protocol" "auto"<br/>    Option   "Device" "/dev/input/mice"<br/>    Option   "Emulate3Buttons" "no"   <br/>    Option   "ZAxisMapping" "4 5"<br/>EndSection* | Seção utilizada principalmente para configuração da **placa de vídeo**. Semelhante ao InputDevice, tem os parâmetros *Identifier* e *Driver* como obrigatórios.<br/><br/>Exemplo:<br/><br/>*Section "Device"<br/>    Identifier "VideoCard0"<br/>    Driver   "nv"   <br/>    VendorName "nVidia"   <br/>    BoardName  "GeForce 6100"   <br/>    VideoRam  131072<br/>EndSection* |



| Monitor                                                      | Screen                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Configurações específicas do monitor utilizado, como *HorizSync* e *VertRefresh*.<br/><br/>Exemplo:<br/><br/>*Section "Monitor"<br/>    Identifier  "Monitor0"<br/>    VendorName  "Monitor Vendor"<br/>    ModelName  "Monitor Model"<br/>    HorizSync  30.0 - 83.0 <br/>    VertRefresh 55.0 - 75.0   <br/>EndSection* | A seção screen é uma combinação entre o monitor e a placa de vídeo,  dizendo ao X quais os modos que ele pode trabalhar. Na sub-seção **Display**, são informados por exemplo as **resoluções** suportadas, **color depth** (bits por pixel), e etc.<br/><br/>*Section "Screen"<br/>    Identifier "Screen0"<br/>    Device   "Card0"<br/>    Monitor  "Monitor0"<br/>    SubSection "Display"<br/>        Viewport  0 0<br/>        Depth   1<br/>    EndSubSection<br/>    SubSection "Display"<br/>        Viewport  0 0<br/>        Depth   4<br/>    EndSubSection<br/>    SubSection "Display"     <br/>        Depth   24     <br/>        Modes  "1920x1080" "1280x1024" "1024x768"   <br/>    EndSubSection   <br/>    SubSection "Display"     <br/>        Depth   8     <br/>        Modes  "1024x768" "800x600" "640x480"   <br/>    EndSubSection<br/>EndSection* |



| ServerLayout                                                 |
| ------------------------------------------------------------ |
| Esta seção agrega as outras definições da configuração do X, associando principalmente as informações do Screen e InputDevices.<br/><br/>Exemplo:<br/><br/>*Section "ServerLayout"<br/>    Identifier   "X.org Configured"<br/>    Screen   0 "Screen0" 0 0<br/>    InputDevice  "Mouse0" "CorePointer"<br/>    InputDevice  "Keyboard0" "CoreKeyboard"<br/>EndSection* |



Nestas aulas estudamos o que é e como funciona o servidor gráfico  X11, como ele é configurado com relação aos dispositivos de entrada,  monitores, resoluções, placas de vídeo e etc.

Noções de Wayland.

Também viu-se como utilizar a variável de ambiente DISPLAY.

Os arquivos e comandos estudados foram:

- /etc/X11/xorg.conf e /etc/X11/xorg.conf.d/
- ~/.xsession-errors
- xhost e xauth - Controle de conexão remoto ao X



#### Controle Gráfico Remoto

```bash
# Habilitar o controle por TCP
# Não é necessário
echo "[X11]\nServerArguments=-listen tcp -dpi 96" >> /etc/sddm.conf

# Setar o controle de Display para o destino
export DISPLAY="ip_destino:0" # Ip:Monitor

# Adicionar permissão para controle remoto

# Opção 1
# No maquina destino executar o comando
xauth list #OutPut: felipe-VirtualBox/unix:0  MIT-MAGIC-COOKIE-1  d69f1b3baf212230cf40265f05060357

# Copiar a saida do comando e alterar o host pelo IP da maquina
# Na maquina Origem adicionar a linha
xauth add ip_destino:0 MIT-MAGIC-COOKIE-1  d69f1b3baf212230cf40265f05060357


# Opção 2
# Adicionar o IP da maquina origem no xhost
xhost +IP_ORIGEM

```



#### Componentes para lembrar

```bash
DISPLAY

xauth # Server X autority file utility
	  list # Lista todos os cookies destinos que tem permissão de usar
	  add  # Adiciona outro cookie destino

xhost # Server access control program for X - Controle por IP
	+ # Desativa o controle por IP
	- # Habilita o controle por IP
	+{IP} # Habilita o controle do IP especifico
```



### 106.2 Desktops Gráficos (1)

#### DE - Desktop Environment

Window Manager 

* Mutter,
* KWin
* Muffin
* Xfwm

#### Principais Ambientes de Desktop

* GNOME
* KDE
* MATE
* XFCE
* LXDE
* Cinnamon

#### Protocolos para acesso remoto a Desktops

* XDMCP - *X Display Manager Control Protocol*
* VNC - *Virtual Network Computing*
* SPICE - 
* RDP - *Remote Desktop Protocol*

### 106.3 Acessibilidade (1)

#### Teclado

Sticky Keys - Para preiconar 2 teclas separadamente

Slow Keys - É necessario precionar a tecla por mais tempo 

Bounce Keys - Debounceing



GOK = Gnome OnBoard KeyBoard - Teclado Virtual

#### Mouse

Use Mouse Emulation - Controle o mouse pelo teclado



#### Visual

High Contrast - Alto Contraste

Fonts Size - Aumenta o tamanho das fontes

KMAG = Screen Magnifier - Lupa para pontos da tela

Screen Readers - ORCA e EmacSpeack



BRLTTY = Braille Display - Teclado para braille



#### Reconhecimento de Voz

CMUSphinx

Simon (KDE)

Julius

## Tópico 107: Tarefas Administrativas

### 107.1 Gerenciamento de Usuários e Grupos - Opções dos Comandos

#### Arquivos

```bash
/etc/passwd # Arquivo de usuários
	# User   : password : UID : GID : Observação,,,  :    home     :  Bash
	
	# mysql  :    x     : 129 : 136 : MySQL Server,,,:/nonexistent :  /bin/false
	# felipe :    x     : 1000: 1000: Felipe,,,      :/home/felipe :  /bin/bash
	
/etc/shadow # Arquivo de senhas 	NÃO PRECISA DECORAR OS CAMPOS
	#felipe:HASH:18489:0:99999:7: : :
	#	|	 |		|  |	|  | | | '-> 	Reserved Field
	#	|	 |		|  |	|  | | '--->	Account expeirantio Date
	#	|	 |		|  |	|  | '----->	Password Inactivity Period
	#	|	 |		|  |	|  '------->	Password Warning Periord
	#	|	 |		|  |	'---------->	Maximum Pass Age
    #	|	 |		|  '--------------->	Minimum Pass Age
    #	|	 |		'------------------>	Date of last Pass Change
    #	|	 '------------------------->	Encrypted Pass
    #	'------------------------------>	Username
   
/etc/group # Arquivo de grupos
	# sudo:x:27:felipe
	
/etc/login.defs # Arquivo de configurações para criação e controle dos usuarios
```



**Principais Opções dos Comandos de Gerenciamento de Usuários e Grupos**

```bash
adduser # Script que chama o useradd
useradd # create a new user or update default new user information
	-c  # *comentário* : Comentário ou Nome Completo do Usuário
 	-d  # *diretório* : Diretório padrão do usuário
 	-s  # *shell* : Shell padrão que será utilizado pelo usuário após seu login
 	-m  # Força a criação do diretório padrão
 	-g  # *grupo* : Define o grupo padrão do usuário
 	-G  # *grupo1, grupo2* : Define o(s) grupo(s) adicionais do usuário
 	-u  # *UID* : Define o ID do usuário
	-e  # *data* : Define uma data de expiração para a conta
	
usermod # Modify User Acount
	-c  # *comentário* : Comentário ou Nome Completo do Usuário
	-d  # *diretório* : Diretório padrão do usuário
	-s  # *shell* : Shell padrão que será utilizado pelo usuário após seu login
	-g  # *grupo* : Altera o grupo padrão do usuário
	-G  # *grupo1, grupo2* : Define o(s) grupo(s) adicionais do usuário
	-a	# Adicionar o grupo, mantendo os anteriores
	-u  # *UID* : Altera o ID do usuário
	-l  # *Login* : Altera um novo nome do login, mantendo o mesmo UID
	-L  # : Trava (lock) a conta. O usuário não consegue logar e nenhuma mensagem de erro é retornada. No arquivo /etc/shadow haverá um símbolo de ! no  início do campo referente à senha.
	-U  # : Destrava a conta. 
	-e  # *data* : Define uma data de expiração para a conta
	
	
userdel # Delete a User account and related files
	-r  # Além das definições do usuário, remove também o diretório home correspondente
	
passwd # Modifica a senha do usuario
```



```bash
groupadd # Adiciona um grupo
	-g # GID: Define o ID do grupo

groupmod # Modify Group

gpasswd  # Adicionar senha ao grupo

newgrp 	 # Altera o grupo padrão do usuario, dentro dos grupos que ele já pertence

chage # Change user password expiry information
	-l # Lista as definições atuais
	-d # DATA : Define forçadamente a data da última mudança de senha. -d0 força a expiração da senha
	-E # DATA : Define a data de expiração da conta. -E0 bloqueia a conta
	-I # dias : Define o número de dias entre a data da expiração da senha e a desativação da conta
	-m # dias : Número mínimo de dias entre as trocas de senha
	-M # dias : Máximo de dias para a alteração de senha. Ou validade da senha.
	-W # dias : Define a partir de quantos dias antes da expiração da senha o usuário receberá avisos
```



#### Comandos

```bash
id # Lista informações do usuarios
	{usuario} #  Lista informações do usuario especificado
	
groups # Lista informações do grupo
	{grupo} # Lista informações do grupo especificado

getent # Lista entidades (informações) gerais
	passwd {usuario}
	group  {usuario}
	
```



### 107.2 Agendamento de Tarefas

#### Crontab

Deamon para execução programada de scripts

```bash
contrab # Comando para cron de usuários especificos
	-l  # List
	-
```

Arquivos

```bash
/etc/crontab

/etc/cron.daily
/etc/cron.hourly
/etc/cron.mouthly

/etc/cron.allow
/etc/cron.deny
```



Formato Arquivo Crontab

```bash
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |   .---------- day of month (1 - 31)
# |  |   |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |   |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |   |  |  |
# *  *   *  *  * user-name command to be executed
  17 *   *  *  *   root    cd / && run-parts --report /etc/cron.hourly
```





#### Comando AT

Executas comandos em um tempo especifico

```bash
at now +2hour
	   +30min
ctrl+d # Para encerrar o script

at 13:20

atq   # Queue of jobs to execute
at -l # /\ Igual

/etc/at.allow
/etc/at.deny
```



#### Systemd Timer

Roda a partir de Units 

```bash
man systemd.timer

man systemd.run # Parecido com o comando AT
```



Formato da Unit

```bash
##   .timer
[Unit]
Description=Daily Apt upgrade and aclean activies
After=apt-daily.timer

[Timer]
OnCalendar=*-*-* 6..18:00,30
RandomizedDelaySec=60m
Persistent=true

[Install]
WantedBy=timers.target


##   .service
[Unit]
Description=Daily Apt upgrade and aclean activies
Documentation=man:apt(8)
ConditionACPower=true
After=apt-daily.service

[Service]
Type=oneshot
ExecStart=/usr/lib/apt/apt.systemd.daily install
KillMode=process
TimeoutStopSec=900
```

**ADICIONAR #!/BIN/BASH NO SCRIPT**

#### Horario e Fuso Horario

 ```bash
timedate

date

/etc/localtime

/usr/share/zoneinfo/

export TZ=America/Chicago # Variavel de TimeZone
 ```



#### Idioma, Linguagem e Codificação

```bash
locale
		-a # All

tzselect # Comando para ajudar na identificação do TZ
/etc/localtime # Link para o horario da maquina

env | grep "^LC"
export LC_ALL=en_US.UTF-8
export LANG=
export LANGUAGE=

file arquivo.txt # Mostra o formato de escrita do arquivo

iconv # Convert file linguagem
	  -f ISO-8859 -t UTF-8 arquivo.txt # Converte de ISO-8859 para UTF-8
```

## Tópico 108: Serviços Essenciais do Sistema

### 108.1 Manutenção do Sistema de Horas (3)

```bash
date
	+%H:%M # Hora e Minuto
	+y 	   # Ano em 2 digitos (2017 = 17)
hwclock # Hardware Clock -> Bios/Placa mãe
	--set --date "12/01/2021 14:00" # Seta o horario da placa mãe
	--hctosys # Sincroniza o date com bios
	-s		  # /\
```

#### NTP Server 

Network Time Protocol Server

Servidor para padronização de horario em um parque de servidores/ cluster.

Objetivo de facilitar a investigação e sincronização de dados./logs

```bash
ntpd
ntpdate # Atualização manual do horario

/etc/services

ntpq -p  # Lista de servidores utilizado e estatisticas
```



**/etc/ntp.conf**

```bash
# Use servers from the NTP Pool Project. Approved by Ubuntu Technical Board
# on 2011-02-08 (LP: #104525). See http://www.pool.ntp.org/join.html for
# more information.
	pool 0.ubuntu.pool.ntp.org iburst # Lista de servidores de busca NTP
    pool 1.ubuntu.pool.ntp.org iburst
    pool 2.ubuntu.pool.ntp.org iburst
    pool 3.ubuntu.pool.ntp.org iburst

```



#### Chrony e timedatectl

```bash
 chrony  
 		tracking
 		sources
 		sources -v
 		
 chronyc # Chrony commando Line
```



```bash
timedatectl # systemd-timesyncd.service
```

### 108.2 Gerenciamento do Sistema de logs (4)

* syslog
* **rsyslog** - Foco LPI
* syslog-ng
* systemd-journal

#### Rsyslog

##### Arquivos

```bash
/etc/rsyslog.conf
	/rsyslog.d/50-default.conf
		# facility.priority		file
		# auth,authpriv.*		/var/log/auth.log
		# mail.err				/var/log/mail.err
# Sequencia de logs
# debug > info > notice > warning > error > crit > alert > emerg/panic
```



#### LogRotate

Compressão dos logs

Coordenado pela cron

```bash
/etc/logrotate 
/etc/logrotate.d/
	#  /var/log/apt/term.log {
    #   rotate 12
    #   monthly
    #   compress
    #   missingok
    #   notifempty
    # }
```



#### Systemd-Journal

```bash
UNIT systemd-journal
man journald.conf


systemd-cat # Envia a saida para journal. -> Parecido com logger

journalctl 
			--vaccum-size # Remove por tamanhao
			--verify 	  # Verifica integridades dos logs
			--sync 		  # Sincroniza dados em memoria com disco (grava em disco)
			

/etc/systemd/journald.conf
/etc/sysmted/journal/socket # socket entre Rsyslol e journal

/var/log/journal
```



### 108.3 Básico de MTA (Mail Transfer Agent) (3)

#### MTA - Mail Transfer Agent

​	Programa resposavel pelo envio e recebimento de mesnagens eletronicas (email)

Opções MTA's

* SendMail
* PostFix
* Exim

#### SMTP - Simple Mail Transfer Protocol

* Protocoloo utilizado para envio de mensagens entre servidores
* Por Padrão utiliza a porta 25



MTA = Servidor SMTP = Servidor de Email



#### **Open Relay**

Um termo relacionado aos servidores de e-mail e ao protocolo SMTP que deve ser conhecido é o termo "**Open Relay**".

Quando um servidor SMTP é configurado de forma que ele permita que qualquer  usuário da Internet o use para enviar e-mails, ele é chamado de Open  Relay.

Nesse caso esse servidor será certamente utilizado para  usos maliciosos, principalmente para envio de e-mails de SPAM e/ou  Phishing.



```bash
mail -s "Titulo do email" destino@gmail.com << OOF
> bla 
> bla
> bla
> EOF

mail # Verifica as mensagens do usuario
mailq # Queue dos emails pendentes


/var/spool/mail

/etc/aliases # Arquivo de configuração de alias para os recebedores dos emails

~/.forward # Arquivo que o usuario pode confiurar para compartilhar seus email recebidos com outrs usuarios
```



### 108.4 Gerenciamento de Impressão de Impressoras (2)

#### CUPS

Servidor para configuração de impressoras

```bash
http://localhost:631 # Servidor Http para configuração via interface grafica

/etc/cups/cupsd.conf	# Configuação servidor
/etc/cups/printers.f	# Configuração impressoras
/etc/cups/ppd/			# Drivers das empressoras 
/var/spool/cups
```



##### Comandos

```bash
lpinfo
		-a # All
		-v # View dispositivos 
		-m # Driver do dispositivo

lpstat 	  # Status
lpq 	  # Status
	-P {device}
	
lpadmin -p {device} -E -v "usb://HP/Deskt...." -m drv://hpcups...

lpoptions


lpq  # Fila de impressoes
lprm # 
lpr

```





## Tópico 109: Fundamentos de Redes

### 109.1 Fundamentos de Protocolos de Internet (4)

| Classe | Primeiro Octeto | Range                       | IPs Privados                  | Máscara Padrão     |
| ------ | --------------- | --------------------------- | ----------------------------- | ------------------ |
| A      | 1-126           | 1.0.0.0 - 126.255.255.255   | 10.0.0.0 - 10.255.255.255     | 255.0.0.0 / 8      |
| B      | 128-191         | 128.0.0.0 - 191.255.255.255 | 172.16.0.0 - 172.31.255.255   | 255.255.0.0 / 16   |
| C      | 192-223         | 192.0.0.0 - 223.255.255.255 | 192.168.0.0 - 192.168.255.255 | 255.255.255.0 / 24 |

IP Publico - IP externo, dado pelo provedor. 1 Por roteador

IP Privado - IPs distribuidos internamente pelo roteador dentro da rede privada. 

| Endereço privado          | Tipo                  |
| ------------------------- | --------------------- |
| Endereço de rede /24      | X.X.0.0               |
| Endereço de Broadcast /24 | X.X.255.255           |
| Hosts /24                 | X.X.0.1 - X.X.255.254 |
|                           |                       |

#### Primeiras 1024 protas são reservadas para servições conhecidos

| Portas    | Serviço      |
| --------- | ------------ |
| 20 e 21   | FTP          |
| 22        | SSH          |
| 23        | TELNET       |
| 25 e 465  | SMTP e SMTPS |
| 53        | DNS          |
| 67 e 68   | DHCP         |
| 80 e 443  | HTTP e HTTPS |
| 110 e 995 | POP3 e POP3S |
| 123       | NTP          |
| 139       | Netbios      |
| 143 e 993 | IMAP e IMAPS |
| 161 e 162 | SNMP         |
| 389 e 636 | LDAP e LDAPS |



#### Abriviação IPv6

2001:0BAA**:0000:0000:0000:**24D2:12AB:98BC

2001:0BAA**:0:0:0:**24D2:12AB:98BC

2001:0BAA**::**24D2:12AB:98BC

**Ou pode ser abriado em partes**

2001:0BAA**:0000:24D2:0000:**24D2:12AB:98BC

2001:0BAA**:0:24D2:0:**24D2:12AB:98BC

2001:0BAA:0000**::24D2::**24D2:12AB:98BC



#### ICMP = Internet Control Message Protocol

Utilizzado para transmitir informações de controle entre os elementos de rede, por exemplo:

* Controle de Volume de tráfego
* Detecção de Desntinos não atingíveis
* Redirecionamento de Rotas
* Verificação de status de hosts remotos

Utilizado em comando como **ping** e **traceroute**

### 109.2 Configurações Per sistentes de Redes (4)

```bash
/etc/hostname
/etc/hosts
/etc/nsswitch.conf # Name Service Switch
/etc/networks
/etc/resoulv.conf

/etc/NetworkManager
/etc/NetworkManager/system-connections # Arquivos de conf para as redes já conectadas

/lib/systemd/network/
/lib/systemd/network/80-container-vz.network


hostname
hostanemctl

nmcli  # Comand line tool for controlling NetworkManager
		device 			# Resumo das interfaces
		device show 	# Detalhes das interfaces
		general status  # Conectividade
		radio			# Status da rede wireless
		network			# Status das redes
		connection		# Status das conexões
			add 		# Add conexão
		con down "{conexão}"

nmcli connection add type ethernet con-name nova-rede ifname enp0s3 ip4 192.168.8.100/24 gw4 192.168.8.1

Unit - NetworkManager # systemctl status NetworkManager - Faz a configuração de redes automaticamente. Busca o servidor DHCP da rede e redistribui os IPs
Unit - systemd-networkd # systemctl status systemd-networkd - Gerenciador optativo 
```



#### Comandos

```bash
# Comandos de configuração NetworkManager ~ nmcli
ifup
ifdown
ifquery
/etc/network/interfaces
```



### 109.3 Resolução de Problemas de Redes (4)

```bash
Pacote iproute # opção ao nettools -> Deprecaited

ip addr|a|address 
		show {interface}
		add {ip} {interface}
		del {ip} {interface}
		flush dev {rede} # Clear ip address 

ip link set {interface} down # Derruba a interface (ip addr show)
						up
ip route show
		 add default via 192.168.1.1 dev {interface} # Adiciona uma rota default
		 	 172.16.30.0/24 via 10.0.0.1 dev enps0s8  # Adiciona uma rota para enviar tudo recebido pela rede 172 para ip 10.0
		 del
		 
## Awakeness
ifconfig
route
```



#### Comandos de Debug

```bash
hostname -d	# Domain
		 -f # -fqdn = Full qualified domain name

ping6
ping # ICMP
	-c # Count packages (-c5, count 5 packages)
# Para o ping funcionar o host deve aceitar o protocolo ICMP

traceroute6 # UDP
traceroute {IP} # Traça a rtoa de servidores acessados até o destino
tracepath		# Tracepath é o mesmo do traceroute, porém não tem as opções de sudo. Pode ser rodado por qualquer usuario
```

 

```bash
ss # Ferramenta para investigar sockets
	-a # Todas. Sockets LISTEN também
	-t # Lista TCP
	-u # Lista UDP
	-l # LISTEN
	-n # Not resolve port names
	
/etc/services # Arquivo de referencia para nome de serviços e portas de referencias ( 80/HTTP )
netstat # Print network connections, route tables, interfaces statistics, masquerade connections

nc
netcat -l -p 1234 # Arbitrary TCP and UDP connections and listens = Ouvi na porta 1234
	-k # Keep Socket
	-l # Listen
	-p # Port
	-v # Verbose
	-z # Apenas testa a conexão = 192.168.17.35 1230-1240 # Testa conexão da porta 1230 até 1240
	{ip} {porta} # Conecta no ip:porta

```



### 109.4 Configuração de Cliente DNS (2) 

```bash
/etc/hosts # Configuração fixa de IP e DNS
/etc/nsswtich.conf # Configuração da ordem de busca e onde buscar
/etc/resolv.conf # Configurações de resolução DNS. Controlado por systemd-resolved

systemd-resolve # Unit para controle dos DNS internos
```

#### Pesquisa de resolução de DNS

```bash
host google.com # Lista os IPs relacionados
	 8.8.8.8	# Lista os DNS relacionados ao nome
	  
dig {DNS} # DNS lookup utility. Usa os servidores listados em /etc/resolv.conf
	@8.8.8.8 # Busca pelo servidor 8.8.8.8 
	
getent # Get Entries from Name Service Switch libraries
```



## Tópico 110: Segurança

#### 110.1 Realizar Tarefas de Segurança do Sistema (3)

```bash
su	{usuario}   	# Altera o login
	- {usuario} 	# Realiza um novo login no usuário especificado
	-c "{comando}"	# Execua o comando com permissão de root

sudo # Execute a command as sudo
	-i # Login
visudo # Edita o /etc/sudoers
```



#### /etc/sudoers

```bash
# % = Grupo.
#	Terminal=(User:Group) Command
#				  '-> Usuários e grupo que pode assumir
# User privilege specification
root    ALL=(ALL:ALL) ALL
# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
```





#### Comandos

```bash
who # Usuários logados
	-a # All
	-h # 
w 	# Usuarios logados e o que estão fazendo
last # Ultimos logins
lastb # Tentativas de logins falhas
lastlog # Mostra o ultimo login de cada usuario do sistema
```



#### Configurações de senhas e usuários

```bash
chage 
	-l {usuario} # List user
	
passwd 
	-S {usuario}
	-w	# Warning para troca de senha
	-i	# Inativate user
	-l	# Lock user
	-u	# Unlock
	
usermod 
	-L	# Lock user
	-U	# Unlock
```



#### Configurações de arquivos

```bash
find
	/ -perm -4000 -ls
		|	   |    '-> Listar
        |      '------> Permissão SUID
        '-------------> Permissions
   /   \( -perm -2000 -o -perm -4000 \) -ls # Busca no "/" Arquivos com permissão SUIG ou GUID
   -nouser	# Arquivos sem usuários validos
```



#### Limites de recursos da mquina para usuarios

```bash
ulimit # Get and set user limits
	-a # All options
	-u # Max user process
	-Hu 1000 # Set Hard max user process 1000
	-Su 500	 # Set Soft Max user process
	# Sem valor Get. Com valor Set /\
	
/etc/security/limits.conf # Usado para setar os limites de forma permanente
	# user		type	option	limit
	usuraio2	soft	nofile	10000
```



#### Redes

```bash
netstat # Print network connections routing table, interface statistics...
	-tulpn # TCP, UDP, LISTEN, Processos, Number ports
	
lsof # List Open Files
	-i # IPs. Conexões da maquina
nmap # Scan das portas ativas e abertas de um host
	10.0.0.1 # Scan do host
	10.0.0.1/24 # Scan de todas as maquinas presentes na rede/mascara

fuser # Monitora uma porta em especifico
	25/tcp # Lista o processo que está utilizando a porta
	-u # Lista o dono do processo
	
```



#### 110.2 Configurações de Segurança do Host (3)

#### Arquivos

```bash
/etc/passwd
	:/bin/false # Usuarios que não podem efetuar login

/etc/nologin # Se existir o arrquivo, nenhum usuário pode fazer login na maquina, apenas o root

/etc/shadow
```

#### Comandos

```bash
pwconv


systemctl disable  # Habilitar e desabilitar serviços
		  enable
		  is-enabled
		  
SysV # Hab e Desab por SysV
	/etc/rc*.d/ # Alterar o nome do link de Sxxx para Kxxx
	/etc/init.d/ # Remover a permissão de execução do script
```



#### Telnet - Inet - TCP Wrapper

**OBSOLETO - NÃO CAI NO EXAME. APENAS XINETD** 

Inet é uma interface entre FTP e Telnet, onde todas as requisições passam primeiro por ele. Após a validação eles repassa a conexão para o devido processo

```bash
# Procolo TELNET - Antigo protocolo de comunicação client-server
inetd

/etc/inetd.conf
	#:STANDARD: These are standard services.
	#Entrada										TCP Wrapper		Saida
    telnet          stream  tcp     nowait  telnetd /usr/sbin/tcpd  /usr/sbin/in.telnetd
    ftp             stream  tcp     nowait  root    /usr/sbin/tcpd  /usr/sbin/in.ftpd
```



##### TCP Wrapper

Primeiro é buscado no allow, se não encontrar nada então busca no deny

```bash
/etc/hosts.allow # Arquivo de permissão de acesso aos serviços internos pela rede
	# ALL: 10.0.0.0 @some_netgroup
	# ALL: .foobar.edu EXCEPT terminalserver.foobar.edu

/etc/hosts.deny # Arquivo de bloqueio

```



##### XInetD

```bash
/etc/xinetd.conf
/etc/xinetd.d/
```



#### Systemd.socket

Substituto do Xinetd. Mantem um socket aberto esperando uma conexão para em seguida executar algum script

```bash
/lib/systemd/system/*.socket
```



##### teste.socket

```bash
[Unit]
Description=Teste Socket

[Socket]
ListenStream=1234
Accept=yes

[Install]
WantedBy=sockets.target

```



##### teste@.service

```bash
[Unit]
Description=Teste de Socket

[Service]
Type=single
ExecStart=/home/felipe/teste-socket.sh
StandardInput=socket

[Install]
WantedBy=socket.target
```

O script a ser executado pode ser qualquer coisa



#### 110.3 Protegendo Dados com Encriptação (4)

#### Pricinpios segurança

* Autenticidade
* Confidencialidade
* Integridade
* Irretratabilidade



#### SSH - Secure Shell

* Protocolo de Criptografia de Rede
* Cria um canal seguro de comunicação entre 2 hosts, um cliente e um servidor
* Utiliza chaves assimetricas, ou seja, um conjunto de chaves publicas e privadas
* OpenSSH é a implementação mais comum



##### Chaves Publicas e Privadas

Chave publica - Compartilhada. Usada para criptografar

Chave Privada -  Privada. Usada para decriptografar a mensagem criptografada pela chave publica



OpenSSH

* Client - Usado para fazer a conexão
* Server - Usado para receber a conexão

```bash
/etc/ssh/
		ssh_config # Configuração CLIENTE
		sshd_config # COnfiguração SERVIDOR
600		ssh_host_dsa_key # Chave Privada  	 - DSA é o tipo de criptografia
644		ssh_host_dsa_key.pub # Chave Publica - 
```



#### Autenticação por senha

```bash
ssh {usuario}@{server} # ssh felipe@google.com
ssh -l {usuario} {server}

    The authenticity of host 'google.com (8.8.8.8)' cant be established.
    ECDSA key fingerprint is SHA256:N10Ksjdnzp293JKa0sj.
    Are you sure you want to continue connecting (yes/no)?
# Primeiro é realizado a tranferencia da chave publica 
```

#### Autenticação por chaves publicas ( Gerar chave publica )

```bash
# Cliente Gera chave publica e privada
ssh-keygen 
		-t rsa -b 1024 #
		-t 
		-b
		
# Server deve adicionar a chave publica no arquivo
~/.ssh/authorized_keys
```

Pela troca das chaves publicas os servidor confia na autenticação realizada pelo cliente.

#### Autenticação por passphrase ( Autenticação dupla )

PassPhrase é o controle de uso das chaves compartilhadas.

Guarda em memoria com o processo **ssh-agent** o controle de acesso.

Para adicionar a permissão para o usuário logado é necessário realiar o comando

```bash
ssh-add 
	{senha}
```

Assim será armazenado em memoria que o usuario pode utilizar as senhas publicas compartilhadas.

Caso não tenha realizado o procedimento é exigido que digite o PassPhrase ao fazer ssh. Caso não tenha a senha é exigido a senha normal de acesso ao SSH

#####  Utilizar PassPhrase

Ao realizar a criação das novas chaves é perguntado

```bash
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
```



#### Comandos

```bash
ssh-keygen
ssh-add # Adiciona em memoria a permissão de uso
ssh-add -D # Delete todas as permiss~oes salvas em memoria
```

#### 

#### Ciphers

Algoritmos utilizados para criar as chaves de criptografia

Tipos:

* dsa
* ecdsa
* ed25519
* **rsa** - Padrão

Padrão de arquivo

```bash
id_{criptografia} # id_rsa   id_rsa.pub
```



Ambos os lados necessitam ter os tipos instalados para realizar a comunicação



#### Tunel SSH

```bash
ssh -N -f -L {porta}:{ip_destino}:{porta} {user}@{ip_destino}
	-N # Do not execute a remote command. This is useful for just forwarding ports.
	-f # Forwading
	-L # Addres
```



```bash
scp
```



#### GPG

Software de criptografia

Garante a criação de chaves, criptografia, decriptografia e assinatura de arquivos.

##### Transferencia de chaves publicas

```bash
gpg --gen-key
gpg --list-keys

#Export/Import opção 1
gpg --export "Comentario" > chave-pub.pub # Exportação binaria da chave
	--output nova-chave.pub --export "Comentario"
	
#Export/Import opção 2	
	--armor # Gera o arquivo em ASCII (Human readable) da pra copiar e colar o conteudo do arquivo
			# Colar o conteudo na pasta do cliente
	
#Export/Import opção 3	
	--keyserver keys.gnupg.net # Utiliza um servidor publico para exportação
    							--send-keys {código chave} # Exporta a chave para o servidor
								--gen-revoke {código chave} #  Revoga a exportação da chave
	
								--recv-keys {código chave}
```



##### Criptografia e descriptografia de dados

```bash
gpg --recipient "Comentario" --output arquivo_cript.gpg --encrypt arquivo.txt
	--output arquivo_dec.txt --decrypt arquivo-cript.gpg
```



##### Assinatura de arquivos

```bash
gpg --sign arquivo.txt
	--verify arquivo.txt.gpg
```



##### Arquivos 

```bash
~/.gnupg/pubring.gph # Arquivos de chaves publicas
```



#### GPG-Agent

Daemon que armazena as chaves secretas e controla o acesso as chaves do sistema