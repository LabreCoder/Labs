# Guia de Tipos de Ataques de Força Bruta (Brute Force)

## 🔐 Conceito Geral

Ataques de força bruta consistem na tentativa sistemática de descobrir credenciais (senhas, chaves ou tokens) por meio de **tentativas repetitivas e automatizadas**. Esses ataques podem ocorrer tanto **online** (contra serviços ativos) quanto **offline** (contra hashes previamente obtidos).

---

## Tipos de Ataques

### 1. Ataque de Dicionário

São ataques baseados em **listas de senhas prontas** (*wordlists*) compostas por combinações comuns, vazadas ou previsíveis.

* 🔧 **Como funciona:**
  A ferramenta percorre uma lista de palavras testando cada uma como senha até encontrar correspondência.

* 📚 **Exemplos de wordlists populares:**

  * `rockyou.txt`
  * `SecLists`
  * Listas personalizadas geradas com base no alvo

* ⚠️ **Ponto fraco explorado:**
  Usuários que utilizam senhas simples ou comuns.

* 🛡️ **Defesa:**

  * Uso de senhas complexas e únicas
  * Políticas de senha robustas
  * Autenticação multifator (MFA)

---

### 2. Ataque de Força Bruta (Permutação)

Testa **todas as combinações possíveis de caracteres**, sem depender de listas pré-existentes.

* 🔧 **Como funciona:**
  Geração automática de combinações possíveis com base em:

  * Tamanho da senha
  * Conjunto de caracteres (letras, números, símbolos)

* 📈 **Complexidade:**
  O número de combinações cresce exponencialmente:

  * Senha de 6 caracteres → milhares de combinações
  * Senha de 12 caracteres → bilhões/trilhões de combinações

* ⚠️ **Limitações:**

  * Alto consumo de tempo e recursos computacionais
  * Pode ser inviável contra senhas longas

* 🛡️ **Defesa:**

  * Limite de tentativas (lockout)
  * CAPTCHA
  * Monitoramento de comportamento suspeito

---

### 3. Ataque Híbrido

Combina **ataque de dicionário + variações inteligentes**.

* 🔧 **Como funciona:**
  Parte de uma palavra base e aplica modificações:

  * Adição de números (`senha123`)
  * Substituições (`@` no lugar de `a`)
  * Prefixos/sufixos (`!senha`, `senha2024`)

* 🧠 **Base conceitual:**
  Explora padrões humanos previsíveis na criação de senhas.

* ⚙️ **Mangling Rules (Regras de Mutação):**
  Transformações aplicadas automaticamente às palavras da lista:

  * Uppercase/lowercase
  * Reverse (inversão)
  * Duplicação

> **🔎 Pesquisar mais a fundo:** Ferramentas como John the Ripper e Hashcat utilizam regras avançadas de mangling altamente eficientes.

---

### 4. Credential Stuffing

Explora **credenciais reais vazadas** em ataques anteriores.

* 🔧 **Como funciona:**

  * Utiliza bases de dados vazadas (ex: dumps de grandes serviços)
  * Testa automaticamente em diversos sistemas

* ⚠️ **Ponto crítico:**
  Reutilização de senha entre diferentes plataformas.

* 📊 **Cenário comum:**
  Um vazamento em um fórum pode comprometer contas bancárias ou e-mails se o usuário reutilizar a senha.

* 🛡️ **Defesa:**

  * Uso de senhas únicas por serviço
  * MFA obrigatório
  * Monitoramento de login por localização/dispositivo

---

### 5. Password Spraying

Testa **uma mesma senha comum em múltiplos usuários**, evitando bloqueios.

* 🔧 **Como funciona:**

  * Escolhe senhas populares (`123456`, `Empresa@123`)
  * Testa contra vários usuários com baixa frequência

* 🎯 **Objetivo:**
  Evitar sistemas de bloqueio por tentativa excessiva em uma única conta.

* 🛡️ **Defesa:**

  * Políticas que bloqueiam IPs suspeitos
  * Monitoramento de tentativas distribuídas
  * Detecção de padrões anômalos

---

### 6. Ataque Offline (Hash Cracking)

Realizado após obtenção de hashes de senha.

* 🔧 **Como funciona:**

  * O atacante trabalha localmente, sem limitação de tentativas
  * Utiliza GPUs para acelerar o processo

* 🔐 **Tipos de hashes comuns:**

  * MD5 (fraco)
  * SHA-1 (obsoleto)
  * SHA-256
  * Bcrypt (mais seguro)

* ⚠️ **Risco elevado:**
  Sem mecanismos de bloqueio, o atacante pode testar milhões de combinações por segundo.

* 🛡️ **Defesa:**

  * Uso de hashing com salt
  * Algoritmos fortes (bcrypt, Argon2)
  * Custos computacionais elevados (*work factor*)

---

## ⚙️ Ferramentas de Auditoria e Exploração

| Ferramenta          | Foco Principal | Diferencial / Observações                                          |
| :------------------ | :------------- | :----------------------------------------------------------------- |
| **HYDRA**           | Redes          | Extremamente rápido; suporta diversos protocolos (SSH, FTP, HTTP). |
| **NCRACK**          | Redes          | Otimizado para redes instáveis e alta latência.                    |
| **JOHN THE RIPPER** | Offline        | Excelente para quebra de hashes; altamente configurável.           |
| **HASHCAT**         | Offline        | Utiliza GPU; considerado um dos mais rápidos do mundo.             |
| **WPScan**          | WordPress      | Identifica usuários, plugins e testa credenciais.                  |
| **PATATOR**         | Modular        | Altamente flexível; ideal para cenários complexos.                 |
| **MEDUSA**          | Redes          | Paralelismo eficiente; alternativa ao Hydra.                       |

---

## 🧱 Boas Práticas de Defesa (Resumo Estratégico)

* 🔑 Utilizar **senhas longas e complexas** (mínimo 12 caracteres)
* 🔁 Nunca reutilizar senhas
* 🔐 Implementar **MFA (Autenticação Multifator)**
* ⏱️ Configurar **limite de tentativas**
* 📊 Monitorar logs de autenticação
* 🧂 Utilizar **hash + salt** para armazenamento
* 🚫 Bloquear IPs suspeitos automaticamente

---

## 📌 Sugestões de Pesquisa (Aprofundamento)

* **HYDRA:** Sintaxe avançada para HTTP POST e formulários autenticados
* **JOHN THE RIPPER / HASHCAT:** Otimização com GPU e regras personalizadas
* **Wordlists:** Como gerar listas customizadas com base em OSINT
* **Rainbow Tables:** Conceito, limitações e impacto do uso de *salt*
* **Ataques distribuídos (Botnets):** Uso em força bruta em larga escala

---

*Organizado por [João Victor Labre/LabreCoder](https://github.com/LabreCoder)*