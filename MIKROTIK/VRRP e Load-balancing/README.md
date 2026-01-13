#### Estes conteúdos foram elaborados com a ajuda do ChatGPT.

# VRRP No Mikrotik

Link do lab :<https://drive.google.com/file/d/1b23DQar7clo1v0TWrgyf99f38U08pdT9/view?usp=sharing>

![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/MIKROTIK/VRRP%20e%20Load-balancing/VRRP.png)

VRRP (Virtual Router Redundancy Protocol) é um protocolo que permite a configuração de redundância de roteadores, garantindo que, caso um roteador falhe, outro assuma o papel de roteador principal sem interrupção significativa para os usuários da rede. Em outras palavras, o VRRP cria um "roteador virtual" compartilhado entre dois ou mais dispositivos, aumentando a disponibilidade e a resiliência da rede.
Como o VRRP Funciona

 Master e Backup: No VRRP, um dos roteadores assume o papel de Master e os outros são Backups. O roteador Master é o responsável por encaminhar o tráfego normalmente. Caso ele falhe, o roteador Backup assume automaticamente o papel de Master.

IP Virtual: O VRRP cria um IP virtual (que é o gateway para os dispositivos na rede). Esse IP é compartilhado entre o Master e os Backups. O roteador Master responde ao IP virtual, enquanto os roteadores Backup ficam em "standby", monitorando o status do Master.

Preempção: Se o Master voltar a ficar ativo após uma falha, ele pode retomar seu papel automaticamente, dependendo da configuração de preempção. Com a preempção habilitada, o roteador com a maior prioridade sempre assumirá o papel de Master quando estiver disponível.

Prioridade: Cada roteador VRRP tem um valor de prioridade, de 1 a 254. O roteador com a maior prioridade se torna o Master. Em caso de empate, o roteador com o maior endereço IP assume o papel.
<br>
<br>

- **Rede LAN**: `10.10.10.0/24`
  - Endereço do Roteador 1: `10.10.10.1`
  - Interface LAN: `ether2-LAN`
- **Rede VRRP**: `192.168.1.0/24`
  - IP Virtual (VRRP): `192.168.1.1`
  - Interface VRRP: `vrrp1`
- **Rede WAN**: `192.168.198.0/24`
  - Endereço do Roteador WAN: `192.168.198.1`
  - Interface WAN: `ether1-WAN`

Abaixo está o guia para configurar o VRRP e o load balancing com esses dados.

---

# Configuração do VRRP no Mikrotik

![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/MIKROTIK/VRRP%20e%20Load-balancing/VRRP%20(2).png)

Vamos configurar dois roteadores para que compartilhem o mesmo IP virtual `192.168.1.1` na rede `192.168.1.0/24`, usando a interface `vrrp1` para redundância de gateway. Em caso de falha de um roteador, o outro assumirá automaticamente o papel de **Master**, mantendo a continuidade de serviço.

#### Passo a Passo: Configuração do VRRP

1. **Roteador 1 (Master para IP Virtual)**

   - **Configurar o VRRP na interface LAN**:

     ```
     /interface vrrp add interface=ether2-LAN vrid=1 priority=150 interval=1 preemption-mode=yes virtual-address=192.168.1.1
     ```

     Aqui, o Roteador 1 tem prioridade maior (150) para o grupo VRRP 1, tornando-o o Master principal para o IP `192.168.1.1`.

   - **Configurar o IP na interface LAN**:

     ```
     /ip address add address=10.10.10.1/24 interface=ether2-LAN
     ```

2. **Roteador 2 (Backup para IP Virtual)**

   - **Configurar o VRRP na interface LAN**:

     ```shell
     /interface vrrp add interface=ether2-LAN vrid=1 priority=100 interval=1 preemption-mode=yes virtual-address=192.168.1.1
     ```

     Aqui, o Roteador 2 tem uma prioridade menor (100), tornando-se o Backup para o grupo VRRP 1 e assumindo o IP `192.168.1.1` apenas se o Roteador 1 falhar.

   - **Configurar o IP na interface LAN**:

     ```shell
     /ip address add address=10.10.10.2/24 interface=ether2-LAN
     ```

#### Explicação da Configuração

- **vrid=1**: Define o identificador de grupo VRRP. Ambos os roteadores devem ter o mesmo VRID para que compartilhem o IP virtual.
- **priority**: A prioridade do Roteador 1 é mais alta, então ele será o Master inicialmente. O Roteador 2 tem uma prioridade menor e será o Backup.
- **virtual-address**: Define o IP virtual `192.168.1.1` que será compartilhado entre os roteadores e usado como o gateway padrão na rede `192.168.1.0/24`.
<br>

<br>

# Configuração do Load Balancing com VRRP

Para distribuir o tráfego entre os dois roteadores, podemos criar um segundo grupo VRRP, cada grupo com seu próprio IP virtual. Assim, metade dos dispositivos da LAN usará um IP virtual como gateway, e a outra metade usará o outro IP.

Link lab :<https://drive.google.com/file/d/1uV116anom6btD2dKXYU5NyLhuMUMYfY9/view?usp=sharing>

![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/MIKROTIK/VRRP%20e%20Load-balancing/VRRP-Load%20balancing%20(4).png)

 **VRRP com Load Balancing** no Mikrotik são os seguintes:

- **Rede LAN**: `10.10.10.0/24`
  - Endereço do Roteador: `10.10.10.2`
  - Interface LAN: `ether2-Lan`
- **IP Virtual do Grupo VRRP 1**: `192.168.1.1/24`
  - Interface VRRP para o primeiro grupo: `vrrp1`
- **IP Virtual do Grupo VRRP 2 (Balanceamento de Carga)**: `192.168.1.2/24`
  - Interface VRRP para o segundo grupo: `VRRP-load-bal`

Esse setup indica dois grupos VRRP, `vrrp1` e `VRRP-load-bal`, cada um com um IP virtual distinto (`192.168.1.1` e `192.168.1.2`). Com esses dados, o balanceamento de carga distribuirá o tráfego entre dois gateways virtuais na rede `192.168.1.0/24`, mantendo a redundância de gateway.

![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/MIKROTIK/VRRP%20e%20Load-balancing/VRRP-Load%20balancing.png)

### Configuração para VRRP com Load Balancing no Mikrotik

#### Passo a Passo: Configuração dos Grupos VRRP

1. **Configuração do Roteador 1**

   - **Grupo VRRP 1 (Gateway Virtual 1)**:

     ```shell
     /interface vrrp add name=vrrp1 interface=ether2-Lan vrid=1 priority=150 interval=1 preemption-mode=yes virtual-address=192.168.1.1
     ```

     Aqui, o Roteador 1 tem uma prioridade mais alta (150), tornando-o o Master para o grupo `vrrp1` com o IP virtual `192.168.1.1`.

   - **Grupo VRRP 2 (Gateway Virtual 2 para Balanceamento de Carga)**:

     ```shell
     /interface vrrp add name=VRRP-load-bal interface=ether2-Lan vrid=2 priority=100 interval=1 preemption-mode=yes virtual-address=192.168.1.2
     ```

     No segundo grupo VRRP, o Roteador 1 tem uma prioridade menor (100), tornando-se o Backup para o IP virtual `192.168.1.2`.

   - **Configuração do IP da Interface LAN**:

     ```shell
     /ip address add address=10.10.10.2/24 interface=ether2-Lan
     ```

2. **Configuração do Roteador 2**

   - **Grupo VRRP 1 (Gateway Virtual 1)**:

     ```shell
     /interface vrrp add name=vrrp1 interface=ether2-Lan vrid=1 priority=100 interval=1 preemption-mode=yes virtual-address=192.168.1.1
     ```

     Neste grupo, o Roteador 2 tem prioridade menor (100), atuando como Backup para o IP virtual `192.168.1.1`.

   - **Grupo VRRP 2 (Gateway Virtual 2 para Balanceamento de Carga)**:

     ```shell
     /interface vrrp add name=VRRP-load-bal interface=ether2-Lan vrid=2 priority=150 interval=1 preemption-mode=yes virtual-address=192.168.1.2
     ```

     No segundo grupo VRRP, o Roteador 2 tem prioridade maior (150), tornando-se o Master para o IP virtual `192.168.1.2`.

   - **Configuração do IP da Interface LAN**:

     ```shell
     /ip address add address=10.10.10.3/24 interface=ether2-Lan
     ```

### Explicação da Configuração

- **VRID e Prioridade**: Em ambos os roteadores, `vrid=1` define o primeiro grupo VRRP (com o IP `192.168.1.1`), e `vrid=2` define o segundo grupo (com o IP `192.168.1.2`). A prioridade define qual roteador é o Master e qual é o Backup para cada IP.
- **virtual-address**: `192.168.1.1` e `192.168.1.2` são os IPs virtuais que os dispositivos na rede usarão como gateways.

### Configuração dos Dispositivos da Rede para Balanceamento de Carga

- Configure metade dos dispositivos na rede `192.168.1.0/24` para usar `192.168.1.1` como gateway (grupo `vrrp1`).
- Configure a outra metade dos dispositivos para usar `192.168.1.2` como gateway (grupo `VRRP-load-bal`).

### Funcionamento do Failover e Balanceamento de Carga

1. **Failover Automático**: Se o Roteador 1 falhar, o Roteador 2 assumirá automaticamente o papel de Master para o IP virtual `192.168.1.1`. Se o Roteador 2 falhar, o Roteador 1 assumirá o IP virtual `192.168.1.2`. Isso garante que os dispositivos sempre tenham um gateway funcional.

2. **Balanceamento de Carga**: O tráfego da rede é dividido entre `192.168.1.1` e `192.168.1.2`, permitindo que ambos os roteadores compartilhem a carga de rede e utilizem melhor a largura de banda disponível.

### Conclusão

O **VRRP com Load Balancing** proporciona alta disponibilidade e otimização de tráfego ao dividir a carga de rede entre dois roteadores e permitir o failover automático. Essa configuração é eficaz para redes que necessitam de redundância e balanceamento de carga, maximizando o desempenho e a disponibilidade.
