#### Estes conteúdos foram elaborados com a ajuda do ChatGPT.

# Bridge em Redes

![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/MIKROTIK/Vlans-Bridge/Screenshot_2.png)


Link documentação oficial: https://drive.google.com/file/d/1oP9Q_acf6WMeIQUB5l5iw-C4gabcYv3y/view?usp=sharing

Link do Lab: https://help.mikrotik.com/docs/spaces/ROS/pages/28606465/Bridge+VLAN+Table


Uma **bridge** é um dispositivo de camada 2 (Enlace de Dados) que conecta diferentes segmentos de rede e encaminha pacotes de dados com base nos endereços MAC (Media Access Control) de origem e destino. Seu papel principal é “filtrar” o tráfego entre segmentos, permitindo que apenas os pacotes necessários sejam encaminhados de um segmento para outro. Isso é útil para:

- **Reduzir o tráfego de broadcast**, uma vez que cada segmento da rede recebe apenas o tráfego necessário.
- **Aumentar a segurança** e o isolamento de dispositivos em diferentes segmentos.
- **Melhorar a eficiência** da rede.

Bridges são usadas frequentemente para segmentar redes em partes menores, o que reduz a carga de tráfego, melhora o desempenho e organiza a rede de maneira mais estruturada.

## Funções e Benefícios de uma Bridge

### 1. Segmentação de Rede
A bridge pode dividir uma rede maior em sub-redes menores (chamadas segmentos). Isso ajuda a reduzir o tráfego de broadcast e a organizar melhor o fluxo de dados, pois cada segmento recebe apenas o tráfego que lhe pertence.

**Exemplo**: Em uma rede corporativa, você pode ter um segmento para o departamento de Vendas e outro para o departamento de TI. Com uma bridge, apenas o tráfego que precisa se mover entre os departamentos será encaminhado, enquanto o restante permanece em cada segmento.

### 2. Filtragem de Tráfego
A bridge examina os endereços MAC dos pacotes para determinar em qual segmento eles devem ser enviados. Caso o endereço MAC de destino esteja no mesmo segmento de origem, o pacote é descartado, reduzindo o tráfego desnecessário entre segmentos.

### 3. Redução de Colisões
Ao dividir a rede em segmentos, as bridges ajudam a reduzir o número de colisões de pacotes. Com menos dispositivos em cada segmento, há menos chances de colisão, o que resulta em uma comunicação mais eficiente.

### 4. Transparência para os Dispositivos
A bridge opera de forma totalmente transparente para os dispositivos finais. Isso significa que os dispositivos na rede não precisam de configurações adicionais para a bridge funcionar — ela faz o encaminhamento e a filtragem dos pacotes automaticamente.

---

# Bridge e VLANs

As **VLANs** (Virtual Local Area Networks) são uma maneira de segmentar logicamente uma rede, independentemente da localização física dos dispositivos. Cada VLAN age como uma “sub-rede” isolada. Usar VLANs com bridges permite uma segmentação física e lógica, sendo uma combinação poderosa para redes maiores.

### Exemplo: Conexão de VLANs com uma Bridge

Suponha que uma empresa tenha três departamentos: Administrativo, Vendas e TI. Cada um tem sua própria VLAN para isolar o tráfego. Em cenários específicos, pode ser interessante criar uma ponte direta entre algumas VLANs sem depender de roteadores.

Por exemplo, se dispositivos da VLAN do departamento de TI (VLAN 30) precisam acessar recursos na VLAN de Vendas (VLAN 20), uma bridge pode ser configurada para permitir a comunicação direta entre essas duas VLANs. Isso é especialmente útil para dispositivos que precisam compartilhar recursos específicos (como impressoras ou servidores), mas sem abrir o tráfego completo entre todas as VLANs.

---

# Bridge em Ambientes com VLANs Tagged e Untagged

Em ambientes com VLANs, o tráfego pode ser marcado (tagged) ou não marcado (untagged) para identificar a que VLAN ele pertence. Bridges podem trabalhar com ambos os tipos de tráfego:

### 1. VLAN Tagged
Quando uma bridge conecta segmentos que têm múltiplas VLANs passando por eles (usando portas tagged), ela pode permitir o tráfego de várias VLANs simultaneamente. Isso é comum em ambientes com múltiplos switches e VLANs, onde uma bridge pode transportar pacotes com diferentes etiquetas de VLAN.

- **Exemplo**: Em uma rede com um switch que suporta várias VLANs, a conexão entre switches pode ser feita com portas tagged. Uma bridge entre esses switches permite o transporte de várias VLANs, mantendo cada uma isolada, mas com possibilidade de comunicação, conforme necessário.

### 2. VLAN Untagged
Em cenários onde uma bridge conecta dispositivos que não reconhecem VLANs, ela pode operar no modo untagged. Isso significa que a bridge trata o tráfego como se não houvesse etiquetas, filtrando pacotes por endereços MAC, mas sem manipulação de VLANs.

- **Exemplo**: Um dispositivo que não suporta VLANs pode ser conectado a uma porta untagged em um switch. A bridge permitirá o tráfego para esse dispositivo sem exigir que ele reconheça VLANs, enquanto o switch ainda consegue controlar o tráfego de outras VLANs.

---

# Vantagens de Usar Bridge com VLANs

### 1. Isolamento e Segurança
Bridges ajudam a manter o tráfego de cada VLAN isolado, mas podem ser configuradas para permitir a comunicação entre VLANs específicas quando necessário. Isso garante que o tráfego entre VLANs fique limitado, mantendo a segurança da rede.

### 2. Flexibilidade
Com bridges, você pode interligar diferentes VLANs e manter o isolamento desejado. Isso oferece um controle maior sobre o fluxo de tráfego, permitindo customizar o acesso entre departamentos e seções da rede.

### 3. Melhor Organização da Rede
Bridges em conjunto com VLANs ajudam a organizar a rede de maneira lógica e mais eficiente. Elas facilitam o gerenciamento e o monitoramento do tráfego, permitindo uma organização mais clara entre setores e departamentos.

---

# Exemplo de Configuração Prática

Vamos considerar um cenário em uma empresa onde o departamento de TI (VLAN 30) precisa se comunicar com alguns servidores na VLAN de Vendas (VLAN 20), mas o restante do tráfego entre VLANs deve ser bloqueado.

Uma bridge pode ser configurada para conectar essas duas VLANs, permitindo que dispositivos de TI acessem os servidores específicos de Vendas. Ao mesmo tempo, o restante do tráfego da VLAN 20 permanece isolado, sem permissão para se comunicar com a VLAN 30. Isso garante que apenas os dispositivos autorizados tenham o acesso necessário.



# Resumo

- **Bridge**: Dispositivo de camada 2 que conecta segmentos de rede, filtrando o tráfego com base em endereços MAC.
- **VLANs e Bridge**: Permite uma segmentação lógica e física da rede, mantendo o tráfego isolado ou integrando segmentos quando necessário.
- **Tagged e Untagged**: Em ambientes complexos, bridges permitem a comunicação entre VLANs tagged, mas também suportam dispositivos que não reconhecem VLANs com portas untagged.
---

# VLANs (Virtual Local Area Networks)


![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/MIKROTIK/Vlans-Bridge/700px-Basic_vlan_switching.jpg)


VLANs são redes locais virtuais que permitem segmentar uma rede física em várias redes lógicas independentes. Isso melhora a segurança, o desempenho e a organização da infraestrutura, especialmente em redes empresariais e data centers. 

## Conceitos-Chave sobre VLANs

### 1. Segmentação e Isolamento
Com VLANs, você pode isolar o tráfego entre diferentes grupos, como departamentos (ex.: Financeiro e RH) em uma mesma rede física. Esse isolamento aumenta a segurança, pois cada VLAN age como uma rede independente.

### 2. Controle de Tráfego e Broadcast
As VLANs limitam o tráfego de broadcast (mensagens enviadas a todos os dispositivos em uma rede), o que reduz o consumo de largura de banda e melhora o desempenho da rede.

### 3. Tipos de VLANs
- **VLAN de Dados**: Usada para separar o tráfego de dados normal dos usuários.
- **VLAN de Voz**: Específica para o tráfego de voz (VoIP), permitindo melhor qualidade de serviço (QoS).
- **VLAN de Gerenciamento**: Usada para acessar e controlar dispositivos de rede, como switches e roteadores.
- **VLAN Nativa**: Normalmente a VLAN padrão de um switch, usada para dados sem tags, mas pode ser alterada conforme necessário.

### 4. Tagging e Untagging
- **802.1Q**: O padrão IEEE 802.1Q permite que um switch adicione tags aos quadros Ethernet, identificando a VLAN a que cada quadro pertence. O tagging insere uma informação no cabeçalho dos quadros, determinando a VLAN específica.
- **Portas Access e Trunk**:
  - **Portas Access**: Associadas a uma única VLAN, geralmente conectadas a dispositivos finais, como computadores.
  - **Portas Trunk**: Permitem o tráfego de múltiplas VLANs entre switches e usam o protocolo 802.1Q para tagging.

## Implementação Prática

### Exemplo em Mikrotik
Em dispositivos Mikrotik, você pode configurar VLANs na interface de rede usando *VLAN tagging* e definir portas de acesso e tronco para controlar o fluxo de tráfego.

Passo a passo:
1. Crie uma interface de VLAN associada a uma interface física.
2. Defina as VLANs desejadas com base no ID de VLAN (número entre 1 e 4094).
3. Configure as interfaces trunk e access conforme a estrutura da rede.

## Exemplo básico de configuração de VLAN no Mikrotik:
#### 1. Cria uma VLAN com ID 10 associada à interface ether1

```
/interface vlan add name=vlan10 vlan-id=10 interface=ether1

```
#### 2. Define a ether2 como uma porta de acesso para a VLAN 10

```
/interface bridge port add bridge=bridge1 interface=ether2 pvid=10

```
#### 3. Configura ether1 como trunk, permitindo o tráfego de várias VLANs

```
/interface bridge vlanadd bridge=bridge1 tagged=ether1 vlan-ids=10
```

Esse exemplo básico configura uma VLAN (com ID 10) e demonstra como definir uma interface trunk e uma interface access em Mikrotik.



Aqui estão as instruções para configurar uma bridge com VLANs em um dispositivo Mikrotik usando a CLI. Esse exemplo cria uma bridge, define VLANs e configura portas com tagged e untagged VLANs para diferentes cenários de conexão.

### Cenário:
1. Criar uma bridge chamada `bridge1`.
2. Configurar três VLANs:
   - **VLAN 10** (Rede de Vendas)
   - **VLAN 20** (Rede de TI)
   - **VLAN 30** (Rede Administrativo)
3. Configurar as interfaces físicas para fazer parte da bridge e aplicar as VLANs:
   - Porta 1: Untagged na VLAN 10 (acesso direto à rede de Vendas).
   - Porta 2: Tagged para todas as VLANs (para conectar a um switch que suporta VLANs).
   - Porta 3: Untagged na VLAN 20 (acesso direto à rede de TI).
   - Porta 4: Untagged na VLAN 30 (acesso direto à rede Administrativa).

### Passo a Passo de Configuração

#### 1. Crie a bridge

```
/interface bridge add name=bridge1
```

#### 2. Adicione as portas físicas à bridge

```bash
/interface bridge port add bridge=bridge1 interface=ether1
/interface bridge port add bridge=bridge1 interface=ether2
/interface bridge port add bridge=bridge1 interface=ether3
/interface bridge port add bridge=bridge1 interface=ether4
```

#### 3. Configure as VLANs na bridge

Aqui, vamos configurar VLAN IDs na bridge e associá-las às portas específicas (tagged e untagged).

##### VLAN 10 - Porta 1 (untagged) e Porta 2 (tagged)

1. Adicione a VLAN 10 à bridge e marque-a como tagged na `ether2` e untagged na `ether1`.

```bash
/interface bridge vlan add bridge=bridge1 vlan-ids=10 tagged=bridge1,ether2 untagged=ether1
```

##### VLAN 20 - Porta 2 (tagged) e Porta 3 (untagged)

2. Adicione a VLAN 20 à bridge e marque-a como tagged na `ether2` e untagged na `ether3`.

```
/interface bridge vlan add bridge=bridge1 vlan-ids=20 tagged=bridge1,ether2 untagged=ether3
```

##### VLAN 30 - Porta 2 (tagged) e Porta 4 (untagged)

3. Adicione a VLAN 30 à bridge e marque-a como tagged na `ether2` e untagged na `ether4`.

```
/interface bridge vlan add bridge=bridge1 vlan-ids=30 tagged=bridge1,ether2 untagged=ether4
```

#### 4. Configure a bridge para filtrar VLANs

Habilite o filtro de VLAN na bridge para garantir que o tráfego seja encaminhado de acordo com as configurações de VLAN:

```
/interface bridge set bridge1 vlan-filtering=yes
```

#### 5. Atribua IPs às VLANs (opcional)

Se precisar de IPs para as VLANs na bridge (por exemplo, para roteamento interno ou administração de VLANs), configure um IP para cada VLAN interface na bridge.

##### Exemplo para VLAN 10:

```
/interface vlan add name=vlan10 interface=bridge1 vlan-id=10
/ip address add address=10.10.10.1/24 interface=vlan10
```

Repita para as VLANs 20 e 30:

```
/interface vlan add name=vlan20 interface=bridge1 vlan-id=20
/ip address add address=10.20.20.1/24 interface=vlan20

/interface vlan add name=vlan30 interface=bridge1 vlan-id=30
/ip address add address=10.30.30.1/24 interface=vlan30
```

---

### Resumo dos Comandos

Esses comandos criam uma bridge (`bridge1`) e configuram VLANs com portas tagged e untagged conforme o cenário descrito. Dispositivos conectados às portas `ether1`, `ether3` e `ether4` acessarão as VLANs de Vendas, TI e Administrativo, respectivamente, enquanto a `ether2` mantém o tráfego de todas as VLANs com tags para conectar a um switch compatível com VLANs.


