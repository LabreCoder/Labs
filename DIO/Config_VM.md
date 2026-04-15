# Passos para Configurar a Máquina Virtual

## 1\. Baixar o VirtualBox

Como minha máquina é Linux Mint, eu consegui instalar diretamente pelo **Software Manager**.

Outra opção é realizar o download direto no site oficial da **VirtualBox (Oracle)**:  
🔗 [virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

Após realizar o download do pacote `.deb`, utilize o comando abaixo para instalar:

```bash
sudo dpkg -i [nome_da_versao_que_voce_baixou].deb
```

-----

## 2\. Baixar as ISOs

### Kali Linux

É necessário realizar o download da ISO específica para o VirtualBox: 🔗 [kali.org/get-kali/\#kali-virtual-machines](https://www.kali.org/get-kali/#kali-virtual-machines)

> **Nota:** O arquivo virá em formato `.zip`. É necessário extraí-lo na pasta de sua preferência.

### Metasploitable

Faça o download do arquivo do Metasploitable 2: 🔗 [sourceforge.net/projects/metasploitable/files/Metasploitable2/](https://sourceforge.net/projects/metasploitable/files/Metasploitable2/)

> **Nota:** Também virá em formato `.zip`, sendo necessária a extração antes do uso.

-----

## 3\. Iniciar as Máquinas Virtuais no VirtualBox

Abaixo estão dois vídeos tutoriais que demonstram como importar essas ISOs para o VirtualBox e o processo de instalação:

  * **Kali Linux:** [Assistir no YouTube](https://youtu.be/Vwe2fl8s7Fg?si=AA9jKEYVmKXXsGMw)
  * **Metasploitable:** [Assistir no YouTube](https://www.youtube.com/watch?v=FprFn0oeEdE0)

-----

## 4\. Configurar a Conexão entre as Máquinas

Utilizei uma rede do tipo **Host-Only Network** para conectar as duas máquinas.

### Passo A: Criar a Rede Host-Only

1.  No VirtualBox, digite `Ctrl + H`.
2.  Selecione **Host-Only Network** e clique em **Create**.
3.  Na aba **Adapter**, selecione `Configure Adapter Manually`.
4.  Vá até a aba **DHCP Server**, marque a opção `Enable Server`.
      * *Obs: Você pode ajustar as faixas de IP (mínimo e máximo) conforme sua preferência.*

### Passo B: Configurar as VMs (Kali e Metasploitable)

Realize os seguintes passos para **ambas** as máquinas:

1.  Selecione a máquina virtual.
2.  Acesse as configurações (**Settings**).
3.  Vá em **Network** (Rede).
4.  Selecione a aba **Adapter 2** e ative-a.
5.  Em "Attached to" (Conectado a), escolha **Host-Only Adapter**.
6.  Selecione o nome da conexão que você criou no Passo A.

-----

## 5\. Acessar as Máquinas e Testar a Conexão

Inicie as máquinas. No terminal de cada uma, digite o comando abaixo para verificar se a interface `eth1` recebeu um IP:

```bash
ifconfig
```

ou

```bash
ip a
```

> [\!IMPORTANT]
> **Nota sobre o Metasploitable:** Ele pode não ativar o DHCP automaticamente. Caso não apareça o IP na `eth1`, execute os comandos abaixo:

1.  Ativar o cliente DHCP temporariamente:
    ```bash
    sudo dhclient eth1
    ```
    - Aqui já será possível ver o IP.
2.  Para tornar a configuração permanente:
    ```bash
    sudo nano /etc/network/interfaces
    ```
    Adicione as seguintes linhas ao arquivo:
    ```text
    auto eth1
    iface eth1 inet dhcp
    ```
3.  Reinicie o serviço de rede:
    ```bash
    sudo /etc/init.d/networking restart
    ```

### Teste Final

Após as configurações, teste a comunicação entre elas usando o comando `ping`:

```bash
ping -c 4 X.X.X.X
```

*(Substitua pelo IP da outra máquina)*. 

Se houver resposta, as máquinas estão conectadas com sucesso e já podemos ir para a execução do exercício\!

---

### Links

- Para visualizar os **objetivos** do exercício, basta clicar *[aqui](./README.md)*.

- Para visualizar o **guia** sobre ataques de Brute Force, basta clicar *[aqui](./Guide.md)*.

- Para visualizar a **resolução** do exercício, basta clicar *[aqui](./Report.md)*.

---

*Organizado por [João Victor Labre/LabreCoder](https://github.com/LabreCoder)*