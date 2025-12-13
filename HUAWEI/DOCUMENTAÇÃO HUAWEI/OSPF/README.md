# OSPF em Roteadores Huawei — Boas Práticas e Exemplo Prático

---

## 1️⃣ Boas práticas de OSPF em roteadores Huawei

### 🔹 Configuração de interfaces

* **Não habilite OSPF em interfaces desnecessárias** (ex.: WAN de cliente).
* **Use autenticação OSPF** (MD5 ou HMAC-SHA256) para maior segurança.
* **Defina corretamente o tipo de rede OSPF**, evitando defaults inadequados:

  * `broadcast` → redes LAN
  * `p2p` → links ponto a ponto (ex.: /30 entre roteadores)

---

### 🔹 Performance e estabilidade

* Utilize **Loopback** para o **Router-ID** (IP fixo e estável).
* Evite **redistribuições excessivas** — anuncie apenas o necessário.

---

## 2️⃣ O que faz o comando `enable traffic-adjustment advertise`

Quando você habilita:

```text
enable traffic-adjustment advertise
```

O roteador passa a utilizar **ajuste dinâmico de custo OSPF baseado em tráfego**:

* Monitora o **uso real de banda** da interface;
* Aplica políticas internas (thresholds/algoritmos);
* **Ajusta automaticamente o custo OSPF** da interface;
* **Anuncia a nova métrica** aos vizinhos OSPF.

📌 Resultado: se um link ficar congestionado, o OSPF recalcula as rotas e **prefere caminhos menos carregados**, aumentando a eficiência da rede.

---

## 3️⃣ Exemplo de configuração OSPF Huawei

### 📘 Cenário

| Roteador | Interface | Rede         | Descrição   |
| -------- | --------- | ------------ | ----------- |
| R1       | GE1/0/0   | 10.0.12.1/30 | Link com R2 |
| R1       | Loopback0 | 1.1.1.1/32   | Router-ID   |
| R2       | GE1/0/0   | 10.0.12.2/30 | Link com R1 |
| R2       | Loopback0 | 2.2.2.2/32   | Router-ID   |

---

### 🖥️ Configuração no R1

```text
system-view
sysname R1

# Loopback para Router-ID
interface LoopBack0
 ip address 1.1.1.1 255.255.255.255
quit

# Interface física ponto-a-ponto
interface GigabitEthernet1/0/0
 ip address 10.0.12.1 255.255.255.252
 ospf network-type p2p
quit

# Processo OSPF
ospf 1
 router-id 1.1.1.1
 enable traffic-adjustment advertise
 import-route direct
 import-route static
 area 0
  network 1.1.1.1 0.0.0.0
  network 10.0.12.0 0.0.0.3
quit
```

---

### 🖥️ Configuração no R2

```text
system-view
sysname R2

interface LoopBack0
 ip address 2.2.2.2 255.255.255.255
quit

interface GigabitEthernet1/0/0
 ip address 10.0.12.2 255.255.255.252
 ospf network-type p2p
quit

ospf 1
 router-id 2.2.2.2
 enable traffic-adjustment advertise
 import-route direct
 import-route static
 area 0
  network 2.2.2.2 0.0.0.0
  network 10.0.12.0 0.0.0.3
quit
```

---

## 4️⃣ Comandos de verificação OSPF

```text
# Ver vizinhanças OSPF
display ospf peer

# Ver rotas aprendidas via OSPF
display ospf routing

# Informações resumidas do processo
display ospf brief

# Interfaces rodando OSPF
display ospf interface

# Rotas instaladas via OSPF
display ip routing-table protocol ospf
```

---

### 🔹 Exemplo de saída — `display ospf peer`

```text
OSPF Process 1 with Router ID 1.1.1.1
Peer      Router ID    Address         State      Priority  Dead Time
0         2.2.2.2      192.168.1.2     Full/DR    1         34
```

---

## 5️⃣ Função do comando `import-route static`

### 📘 Cenário

O roteador **R1** possui uma rota estática para a rede do cliente:

* `192.168.10.0/24`

Essa rede **não participa diretamente do OSPF**, mas precisa ser anunciada para os demais roteadores.

### 🔹 Configuração

```text
[R1 ospf 1]
 import-route static
 import-route direct
```

📌 Isso faz com que:

* Rotas **estáticas** sejam redistribuídas no OSPF;
* Rotas **diretamente conectadas** também sejam anunciadas.

---

## 6️⃣ Redistribuir rota default no OSPF

Para anunciar a rota padrão (0.0.0.0/0) aos vizinhos OSPF:

```text
[R1 ospf 1]
 default-route-advertise always
```

📌 Muito usado quando:

* O roteador tem saída para Internet;
* Os demais roteadores precisam usar esse caminho como **gateway padrão**.

---

## 🧭 Resumo rápido

* **Router-ID** → sempre Loopback
* **Links P2P** → `ospf network-type p2p`
* **Ajuste dinâmico de custo** → `enable traffic-adjustment advertise`
* **Redistribuição** → somente o necessário
* **Default route no OSPF** → `default-route-advertise always`
