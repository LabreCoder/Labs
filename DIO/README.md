# 💻 Guia Prático — Brute Force com Medusa em Ambiente Controlado

## 🎯 Objetivos do Exercício

Este laboratório tem como propósito desenvolver, de forma disciplinada, a compreensão prática de ataques de **força bruta**, utilizando a ferramenta **Medusa** contra um ambiente vulnerável e controlado, como o **Metasploitable**.

Ao longo do exercício, serão explorados conceitos de:

* Enumeração de serviços
* Descoberta de credenciais
* Exploração de autenticação fraca
* Integração entre ferramentas de pentest

---

## ⚙️ 1. Aprender como utilizar o Medusa

O Medusa é uma ferramenta robusta para execução de ataques de autenticação via força bruta em diversos protocolos.

### Comando Base

```bash
medusa -h X.X.X.X \
-U users_list.txt \
-P passwords_list.txt \
-M ftp \
-t 6
```

### Parâmetros explicados

* `-h` → Define o alvo (host)
* `-U` → Arquivo contendo lista de usuários
* `-P` → Arquivo contendo lista de senhas
* `-M` → Módulo/protocolo (ftp, ssh, smb, http, etc.)
* `-t` → Número de threads simultâneas (paralelismo)

---

### Boas práticas no uso

* Iniciar com poucas threads (`-t 2` ou `-t 4`) para evitar bloqueios
* Monitorar respostas do servidor (timeout, reset, bloqueio)
* Utilizar listas de credenciais realistas (ex: `admin`, `user`, `test`)

---

### Observações técnicas

* O Medusa trabalha com **ataques online**, ou seja, depende da resposta do serviço
* Pode gerar logs úteis para análise posterior
* Suporta autenticação paralela eficiente, porém pode ser detectado facilmente

---

## 🌐 2. Descobrir os Serviços Ativos na Rede

Antes de qualquer tentativa de autenticação, é necessário compreender o terreno.

### Utilizando Nmap

```bash
nmap -sV -p- X.X.X.X
```

### Explicação

* `-sV` → Detecta versões dos serviços
* `-p-` → Escaneia todas as portas (1–65535)

---

### 🎯 Objetivo

Identificar serviços como:

* FTP (porta 21)
* SSH (porta 22)
* SMB (porta 139/445)
* HTTP (porta 80)

---

### Dica estratégica

Cada serviço aberto representa uma possível porta de entrada.
O olhar atento transforma um simples scan em um mapa de oportunidades.

---

## 📂 3. Obter Acesso ao FTP

Com o serviço identificado, inicia-se o ataque de força bruta.

### Exemplo

```bash
medusa -h X.X.X.X -U users.txt -P passwords.txt -M ftp
```

---

### 🎯 Objetivo

* Descobrir credenciais válidas
* Acessar o serviço FTP

---

### Após o acesso

```bash
ftp X.X.X.X
```

* Verificar arquivos disponíveis
* Procurar informações sensíveis
* Baixar arquivos relevantes

---

### Possíveis achados

* Arquivos de configuração
* Credenciais armazenadas
* Scripts vulneráveis

---

## 🕸️ 4. Acesso ao DVWA (Damn Vulnerable Web Application)

O DVWA é uma aplicação propositalmente vulnerável, ideal para estudo.

---

### Enumeração com enum4linux

```bash
enum4linux -a X.X.X.X
```

---

### 🎯 Objetivo

* Identificar usuários do sistema
* Coletar informações via SMB
* Descobrir credenciais reutilizadas

---

### Possível acesso

Após obter usuários:

* Testar login manual no DVWA
* Ou utilizar Medusa contra HTTP (caso aplicável)

---

### Insight

Muitas vezes, a falha não está na complexidade técnica, mas na **reutilização descuidada de credenciais**.

---

## 🗂️ 5. Enumeração e Acesso via SMB

O protocolo SMB é frequentemente negligenciado, mas guarda valiosas portas abertas.

---

### Descobrir usuários

```bash
enum4linux -U X.X.X.X
```

---

### Testar acesso com Medusa

```bash
medusa -h X.X.X.X -U users.txt -P passwords.txt -M smbnt
```

---

### 🎯 Objetivo

* Identificar usuários válidos
* Testar credenciais fracas
* Obter acesso a compartilhamentos

---

### Após o acesso

Utilizar:

```bash
smbclient -L //X.X.X.X
```

---

### Possíveis riscos explorados

* Compartilhamentos abertos
* Falta de autenticação
* Permissões mal configuradas

---

## Conclusão Estratégica

Este exercício revela uma verdade silenciosa, porém constante:

> **A segurança raramente falha por complexidade — falha por descuido.**

A combinação de:

* Senhas fracas
* Serviços expostos
* Falta de monitoramento

abre caminhos que ferramentas como o Medusa apenas percorrem.

---

### Links

- Para visualizar o **guia** sobre ataques de Brute Force, basta clicar *[aqui](./Guide.md)*.

- Para visualizar como **configurar a VM**, basta clicar *[aqui](./Config_VM.md)*.

- Para visualizar a **resolução** do exercício, basta clicar *[aqui](./Report.md)*.

---

*Organizado por [João Victor Labre/LabreCoder](https://github.com/LabreCoder)*