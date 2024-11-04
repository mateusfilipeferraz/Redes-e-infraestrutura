# PPPoE (Point-to-Point Protocol over Ethernet) no Mikrotik

![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/PPPOE-Mikrotik/PPPoe.png)

O PPPoE (Point-to-Point Protocol over Ethernet) é um protocolo amplamente usado em redes de provedores de internet para gerenciar autenticação e alocação de endereços IP para clientes conectados a uma rede Ethernet. No Mikrotik, o PPPoE é frequentemente utilizado para implementar redes de acesso a clientes, fornecendo controle de banda, autenticação e gerenciamento de sessão para usuários de forma centralizada.

## Como o PPPoE funciona no Mikrotik

O PPPoE cria uma conexão ponto-a-ponto (Point-to-Point) sobre a camada Ethernet, o que permite autenticar cada cliente individualmente, geralmente usando um nome de usuário e uma senha. No Mikrotik, o PPPoE pode ser configurado em duas partes principais:

1. **PPPoE Server (Servidor PPPoE)**: onde o Mikrotik atua como o servidor de autenticação e gerencia as conexões de cada cliente.
2. **PPPoE Client (Cliente PPPoE)**: onde o Mikrotik se conecta a um servidor PPPoE externo, geralmente em um cenário em que o Mikrotik atua como o roteador do cliente.

## Configuração Básica do Servidor PPPoE no Mikrotik

1. **Criar uma Interface PPPoE Server**:
   - Acesse `PPP > PPPoE Server > Interfaces`.
   - Clique em *Add New* e configure:
     - **Interface**: Escolha a interface física na qual o servidor PPPoE será ativo.
     - **Service Name**: Nome do serviço PPPoE que os clientes usarão para autenticação.
     - **Max MTU** e **Max MRU**: Valores de tamanho máximo do pacote (geralmente 1480 é padrão).

2. **Configurar o Pool de IPs para os Clientes**:
   - Em `IP > Pool`, adicione um pool de endereços IP para que os clientes PPPoE recebam IPs automaticamente.

3. **Criar Perfis de Autenticação (opcional)**:
   - Em `PPP > Profiles`, crie perfis específicos com controles de banda, como limites de upload e download, configurando opções como **Rate Limit**.

4. **Adicionar Usuários**:
   - Vá para `PPP > Secrets` e crie usuários individuais:
     - **Name** e **Password**: Esses serão os dados de autenticação para cada cliente.
     - **Service**: Selecione *PPPoE*.
     - **Profile**: Escolha o perfil de limite de banda (opcional).
     - **Local Address** e **Remote Address**: Defina o IP local (para o Mikrotik) e o IP remoto (para o cliente, que pode ser automaticamente do pool de IPs).

## Configuração do Cliente PPPoE no Mikrotik

Em alguns casos, o Mikrotik se conecta a um servidor PPPoE (por exemplo, ao usar um serviço de internet ADSL).

1. Vá até `PPP > Interfaces` e selecione *PPPoE Client*.
2. Defina:
   - **Interface**: Escolha a interface física onde o Mikrotik está conectado ao modem ou rede do provedor.
   - **User** e **Password**: Credenciais fornecidas pelo provedor.
   - **Add Default Route**: Ative essa opção para adicionar automaticamente a rota padrão, permitindo que o Mikrotik gerencie a saída de rede.
   - Clique em *Apply* e depois em *OK*.

## Recursos Avançados do PPPoE no Mikrotik

- **QoS e Limite de Banda**: Ao definir perfis, você pode limitar a largura de banda, garantindo que cada cliente tenha uma quantidade específica de banda e melhor controle da rede.
- **Monitoramento de Sessão e Controle de Conexões**: Ferramentas como *Queue Tree* e *Simple Queues* podem ser usadas para monitorar e gerenciar o uso de banda dos usuários PPPoE.
- **Autenticação RADIUS**: Em redes maiores, o RADIUS pode ser usado para autenticar os usuários PPPoE em vez do próprio Mikrotik. Isso permite gerenciar contas de usuários centralmente, comum em provedores de internet.

O PPPoE é muito útil em provedores e redes corporativas, pois possibilita a autenticação individual, o controle de banda e a segmentação de clientes de forma eficaz.

```markdown
# Comandos CLI para Configuração PPPoE no Mikrotik

Aqui estão os comandos CLI para configurar tanto o servidor quanto o cliente PPPoE no Mikrotik.

---

### Configuração do Servidor PPPoE no Mikrotik

Esses comandos configuram um servidor PPPoE no Mikrotik que distribui IPs a partir de um pool e aplica um controle básico de banda para cada cliente.

1. **Crie o Pool de IPs**:
   ```shell
   /ip pool add name=Clientes-PPPoE ranges=192.168.10.2-192.168.10.254
   ```

2. **Configure o Perfil de PPPoE**:
   ```shell
   /ppp profile add name=PPPoE-Default local-address=192.168.10.1 remote-address=Clientes-PPPoE rate-limit=2M/4M dns-server=8.8.8.8
   ```

3. **Adicione a Interface do Servidor PPPoE**:
   ```shell
   /interface pppoe-server server add interface=ether1 service-name=PPPoE-Service disabled=no max-mtu=1480 max-mru=1480
   ```

4. **Adicione Clientes PPPoE**:
   ```shell
   /ppp secret add name=cliente1 password=senha123 profile=PPPoE-Default service=pppoe
   /ppp secret add name=cliente2 password=senha456 profile=PPPoE-Default service=pppoe
   ```

---

### Configuração do Cliente PPPoE no Mikrotik

Essa configuração é para um Mikrotik que vai se conectar a um servidor PPPoE, como em um cenário onde ele atua como roteador de um cliente conectado ao provedor de internet via PPPoE.

1. **Configure o Cliente PPPoE**:
   ```shell
   /interface pppoe-client add name=PPPoE-Client interface=ether1 user=cliente1 password=senha123 disabled=no add-default-route=yes use-peer-dns=yes
   ```

   - **interface**: Defina a interface que conecta ao provedor.
   - **user** e **password**: Nome de usuário e senha fornecidos pelo servidor PPPoE.
   - **add-default-route=yes**: Adiciona automaticamente a rota padrão para o gateway PPPoE.
   - **use-peer-dns=yes**: Usa o DNS fornecido pelo servidor PPPoE.

---

Esses comandos configuram um servidor PPPoE no Mikrotik para gerenciar e autenticar clientes diretamente no roteador, sem um servidor RADIUS, e também permitem configurar um cliente PPPoE para se conectar a um provedor.

Se precisar de ajustes para controle de banda, mais clientes ou um setup específico, posso ajudar a adaptar esses comandos!
```
