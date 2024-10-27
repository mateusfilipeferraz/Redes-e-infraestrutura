### Primeira Imagem (WireGuard-Matriz)

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
