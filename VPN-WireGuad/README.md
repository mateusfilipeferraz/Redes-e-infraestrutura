#### Tutorial de como criar o VPN-WIREGUARD

Link download do lab: https://drive.google.com/file/d/1U5GaLAN3LGdLTUFIMk2McXjaC2uiZUiV/view?usp=sharing

### Passo 1: Criar Interface WireGuard em Cada Roteador

1. Acesse o Mikrotik via WinBox.
2. Vá para **Interfaces** > **WireGuard** e clique no botão **+** para adicionar uma nova interface WireGuard.
3. Configure a Interface:
   - **Name**: Defina um nome para a interface (ex.: `WireGuard-Matriz` na Matriz e `WireGuard-Filial` na Filial).
   - **Listen Port**: Escolha uma porta para o WireGuard (ex.: `25365`).
   - **Private Key**: Clique em **Generate** para gerar a chave privada. A chave pública será derivada dela.
4. Clique em **Apply** e depois em **OK**.

---

### Passo 2: Configurar o Peer (Configuração de Par)

Após criar a interface, configure os peers para permitir a comunicação entre os roteadores.

- **No Mikrotik da Matriz**:
  - Com a interface WireGuard criada, clique na aba **Peers** dentro de **WireGuard** e pressione **+** para adicionar um peer.
  - **Interface**: Selecione a interface WireGuard que você criou (ex.: `WireGuard-Matriz`).
  - **Public Key**: Insira a chave pública do roteador da Filial. Para isso, copie a chave pública da interface WireGuard no Mikrotik da Filial e cole aqui.
  - **Endpoint**: Insira o IP público do roteador da Filial (ex.: `192.168.122.76`).
  - **Endpoint Port**: Defina a porta de escuta do WireGuard no roteador da Filial (ex.: `25365`).
  - **Allowed Address**: Insira os endereços IP permitidos pela conexão. No exemplo, `192.168.10.1/24` (rede da Filial) e `10.10.10.2/30` (rede do túnel WireGuard).
  - **Persistent Keepalive**: Defina o intervalo de keepalive para 25 segundos (opcional).

- **No Mikrotik da Filial**:
  - Vá para **WireGuard** > **Peers** e clique em **+**.
  - **Interface**: Escolha a interface WireGuard que você criou na Filial (ex.: `WireGuard-Filial`).
  - **Public Key**: Insira a chave pública do roteador da Matriz. Copie a chave pública da interface WireGuard da Matriz e cole aqui.
  - **Endpoint**: IP público do roteador da Matriz (ex.: `192.168.198.148`).
  - **Endpoint Port**: Porta de escuta do WireGuard na Matriz (ex.: `25365`).
  - **Allowed Address**: Insira os endereços IP permitidos pela conexão. No exemplo, `192.168.1.1/24` (rede da Matriz) e `10.10.10.2/32` (rede do túnel WireGuard).
  - **Persistent Keepalive**: Defina o intervalo de keepalive para 25 segundos (opcional).

---

### Passo 3: Configurar Rotas

Para garantir que o tráfego seja direcionado corretamente, adicione rotas que apontem para o IP da interface WireGuard em cada Mikrotik.

- **No Mikrotik da Matriz**:
  - Vá para **IP** > **Routes** e clique em **+**.
  - **Dst. Address**: Insira o endereço da rede da Filial (ex.: `192.168.10.0/24`).
  - **Gateway**: Coloque o IP da interface WireGuard da Filial (ex.: `10.10.10.2`).

- **No Mikrotik da Filial

### Primeira Imagem (WireGuard-Matriz)

![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/VPN-WireGuad/R1.png)


- **Interface**: Nome da interface WireGuard usada para a conexão, neste caso, chamada de `WireGuard-Matriz`.
- **Public Key**: Chave pública do peer (roteador remoto).
- **Endpoint**: IP público do peer (roteador remoto) ao qual o roteador atual se conecta. Aqui é `192.168.198.148`.
- **Endpoint Port**: Porta usada pelo WireGuard no peer remoto, que é `25365`.
- **Allowed Address**: Intervalo de endereços IP que podem ser alcançados por essa conexão. No exemplo, estão configurados `192.168.10.1/24` e `10.10.10.2/30`, indicando que o roteador atual permitirá tráfego para esses IPs através do túnel.
- **Persistent Keepalive**: Tempo de intervalo de verificação de conectividade, definido para `00:00:25`. Este valor ajuda a manter a conexão ativa.
- **Rx/Tx**: Quantidade de dados recebidos (`1164 B`) e enviados (`2276 B`) por essa conexão.
- **Last Handshake**: Mostra quando ocorreu o último handshake entre os dispositivos, que indica a conectividade e sincronização de chaves.

---

### Segunda Imagem (WireGuard-Filial)
![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/VPN-WireGuad/R2.png)


- **Interface**: Nome da interface WireGuard usada na filial, chamada de `WireGuard-Filial`.
- **Public Key**: Chave pública do peer (roteador da matriz).
- **Endpoint**: IP público do peer, `192.168.122.76`.
- **Endpoint Port**: Porta usada pelo WireGuard no peer, `25365`.
- **Allowed Address**: Intervalo de endereços IP aceitos pela conexão. Configurado com `192.168.1.1/24` e `10.10.10.2/32`, indicando o tráfego permitido pela conexão.
- **Persistent Keepalive**: Valor de keepalive de `00:00:25`.
- **Rx/Tx**: Quantidade de dados recebidos (`2212 B`) e enviados (`1076 B`).
- **Last Handshake**: Último handshake com o peer, mostrando uma conectividade ativa.

---

### Explicação Geral

Essas configurações são para estabelecer um túnel VPN entre duas redes (Matriz e Filial) usando o protocolo WireGuard. A interface WireGuard permite comunicação segura entre esses dois dispositivos Mikrotik, utilizando criptografia e chaves públicas/privadas para autenticação. O **endpoint** e o **endpoint port** indicam para onde o tráfego é direcionado, enquanto o **allowed address** define quais redes podem se comunicar pela VPN. O **keepalive** é usado para manter o túnel ativo mesmo sem tráfego contínuo, e o **handshake** indica que a conexão está estável.
