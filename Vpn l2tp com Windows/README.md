Com certeza\! Aqui está a versão em **Markdown** do seu guia de configuração de VPN L2TP/IPsec no Mikrotik e Windows, formatada para ser clara e fácil de ler no GitHub.

-----

# 🚀 Guia de Configuração VPN L2TP/IPsec (Mikrotik e Windows)

[cite\_start]Este guia detalha os passos para configurar um servidor VPN **L2TP/IPsec** no roteador **Mikrotik** e a respectiva conexão no cliente **Windows**[cite: 1, 3].

-----

## 1\. Configuração no Mikrotik

### 1.1. Crie um IP Pool (Opcional, mas Recomendado)

Crie um pool de endereços IP que será alocado dinamicamente aos clientes VPN.

| Comando/Janela | Campo | Valor/Ação |
| :--- | :--- | :--- |
| **IP** \> **Pool** \> **Add** | **Name** | `pool-vpn` (Nome sugerido) |
| | **Ranges** | `10.10.20.10-10.10.20.50` (Exemplo: Defina um intervalo que não conflite com sua rede local) |

### 1.2. Crie um Profile (Perfil PPP)

[cite\_start]Crie um perfil para definir as configurações de conexão[cite: 5].

  - Na janela **PPP** \> **Profiles**, clique em **Add New**.

| Aba | Campo | Valor/Ação | Fonte |
| :--- | :--- | :--- | :--- |
| **General** | **Name** | [cite\_start]`profile1` [cite: 6, 9] |
| **General** | **Local Address** | [cite\_start]`10.10.1.1` (Use um IP não utilizado em sua rede local) [cite: 10, 36] |
| **General** | **Remote Address** | [cite\_start]`pool-vpn` (Nome do Pool criado ou `Otp` se estiver usando uma configuração estática) [cite: 11] |
| **General** | **Bridge Learning** | [cite\_start]`default` [cite: 21] |
| **Protocols** | **Change TCP MSS** | [cite\_start]`default` [cite: 31] |
| **Protocols** | **Use UPnP** | [cite\_start]`default` [cite: 32] |

[cite\_start]*Clique em **Apply** e depois em **OK**[cite: 34, 35].*

### 1.3. Crie um Secret (Usuário VPN)

[cite\_start]Crie o usuário e senha que o cliente Windows usará para se conectar[cite: 37].

  - Na janela **PPP** \> **Secrets**, clique em **Add New**.

| Campo | Valor/Ação | Fonte |
| :--- | :--- | :--- |
| **Name** | [cite\_start]`julia` [cite: 38, 41] |
| **Password** | [cite\_start]`********` (Defina uma senha segura) [cite: 42] |
| **Service** | [cite\_start]`l2tp` [cite: 43] |
| **Profile** | [cite\_start]`profile1` [cite: 45] |
| **Enabled** | (Checado) [cite\_start][cite: 39] |

*Clique em **Apply** e depois em **OK**.*

### 1.4. Crie o L2TP Server

[cite\_start]Habilite e configure o servidor L2TP[cite: 57, 58].

  - Na janela **PPP** \> **Interface**, clique em **L2TP Server**.

| Aba | Campo | Valor/Ação | Fonte |
| :--- | :--- | :--- | :--- |
| **General** | **Enabled** | (Checado) [cite\_start][cite: 60] |
| **General** | **Max MTU** | [cite\_start]`1450` [cite: 61] |
| **General** | **Max MRU** | [cite\_start]`1450` [cite: 62] |
| **General** | **Keepalive Timeout** | [cite\_start]`30` [cite: 65] |
| **General** | **Default Profile** | [cite\_start]`profile1` [cite: 66] |
| **General** | **Authentication** | [cite\_start]Marque as opções de autenticação (e.g., `mschap2`, `chap`) [cite: 68, 69, 71] |
| **General** | **Use IPsec** | [cite\_start]`required` [cite: 73] |
| **General** | **IPsec Secret** | [cite\_start]`********` (Defina sua chave de segurança pré-compartilhada) [cite: 74] |
| **General** | **Caller ID Type** | [cite\_start]`ip address` [cite: 75] |
| **General** | **One Session Per Host** | (Checado) [cite\_start][cite: 76] |

[cite\_start]*Clique em **Apply** e depois em **OK**[cite: 79, 80].*

-----

## 2\. Configuração no Windows

[cite\_start]Configure a conexão VPN no sistema operacional Windows[cite: 81].

1.  Vá para as **Configurações de VPN** do Windows e clique em **Adicionar uma conexão VPN**.

| Campo | Valor/Ação | Fonte |
| :--- | :--- | :--- |
| **Connection name** | [cite\_start]`vpn12tp` [cite: 85] |
| **Server name or address** | [cite\_start]`10.10.10.113` *(Coloque o **IP público** do seu roteador Mikrotik)* [cite: 88, 102] |
| **VPN type** | [cite\_start]`L2TP/IPsec with pre-shared key` [cite: 90] |
| **Pre-shared key** | [cite\_start]`********` *(Coloque a mesma **IPsec Secret** configurada no Mikrotik)* [cite: 92, 103] |
| **Type of sign-in info** | [cite\_start]`Username and password` [cite: 95] |
| **Username (optional)** | [cite\_start]`julia` [cite: 97] |
| **Password (optional)** | [cite\_start]`********` [cite: 99] |

2.  [cite\_start]Clique em **Save**[cite: 100].

### 2.1. Opcional: Desativar Default Gateway e Criar Rota

[cite\_start]Para evitar que todo o tráfego de internet (navegação) saia pelo túnel VPN (matriz), você deve desativar o *gateway* padrão e adicionar uma rota específica para a rede remota[cite: 104].

1.  **Desativar o Gateway Padrão Remoto:**

      * Vá em **Adaptador VPNs** \> **Propriedades** \> **Networking** \> **Internet Protocol Version 4 (IPv4)** \> **Advanced**.
      * [cite\_start]Desmarque a opção **"Use default gateway on remote network"**[cite: 104].

2.  **Criar Rota Estática no Windows:**

      * Abra o **Prompt de Comando** ou **PowerShell** como administrador.
      * Execute o comando de rota, adaptando os endereços IP para o seu ambiente:

    <!-- end list -->

    ```bash
    route add 10.10.2.1 255.255.255.255.0 192.168.1.254 -p
    ```

    Onde:

      * [cite\_start]`10.10.2.1` é a interface **LAN do Mikrotik** (ou a rede/IP remoto que você deseja acessar)[cite: 107].
      * [cite\_start]`255.255.255.255.0` é a **máscara de rede**[cite: 107].
      * [cite\_start]`192.168.1.254` é o **Local Address** que o Windows recebeu ao conectar a VPN[cite: 108].
      * O parâmetro `-p` torna a rota persistente após reinicializações.