

# 🚇 Túnel MPLS-TE (Traffic Engineering) — Huawei

O **MPLS-TE** permite criar túneis com caminhos controlados, garantindo **engenharia de tráfego**, **redundância**, **priorização** e **uso otimizado da rede**.

---

## 🧱 1️⃣ Configuração base do MPLS-TE

```text
system-view
mpls lsr-id 1.1.1.1
mpls
 mpls te
 label advertise non-null
 mpls rsvp-te
 mpls te cspf
```

### 🔎 Explicação dos comandos

* **mpls lsr-id 1.1.1.1**
  Define o identificador lógico do roteador MPLS (normalmente a Loopback).

* **mpls te**
  Habilita Traffic Engineering no MPLS.

* **label advertise non-null**
  Anuncia labels reais (evita implicit-null).

* **mpls rsvp-te**
  Ativa RSVP-TE, responsável pela sinalização dos túneis.

* **mpls te cspf**
  Ativa o algoritmo **CSPF (Constraint Shortest Path First)**, que considera:

  * Largura de banda
  * Métricas TE
  * Restrições administrativas

---

## 🧭 2️⃣ Caminho explícito (Explicit Path)

```text
explicit-path PE1-P2
 next hop 10.0.0.5
 next hop 10.0.0.1
```

### 📌 Função

* Define manualmente o caminho que o túnel deve seguir.
* O tráfego passará **obrigatoriamente** pelos hops informados.
* Muito usado para:

  * Redundância controlada
  * Evitar links congestionados
  * Engenharia de tráfego avançada

---

## 🌐 3️⃣ Interfaces físicas (Core MPLS)

```text
interface GigabitEthernet0/0/0
 ip address 10.0.0.9 255.255.255.252
 ospf network-type p2p
 mpls
 mpls te
 mpls rsvp-te
 mpls ldp
```

### 🔎 Explicação

* Interface participa:

  * Do **OSPF**
  * Do **MPLS**
  * Do **RSVP-TE**
  * Do **LDP**
* Necessário para:

  * Distribuição de labels
  * Sinalização do túnel
  * Cálculo CSPF

📌 **Todas as interfaces do core MPLS devem ter esses comandos.**

---

## 🚇 4️⃣ Criando o Túnel MPLS-TE

```text
interface Tunnel0/0/0
 description Com-Redundancia
 ip address unnumbered interface LoopBack0
 tunnel-protocol mpls te
 destination 5.5.5.5
 mpls te tunnel-id 1
 mpls te record-route
 mpls te path explicit-path PE1-P2
 mpls te backup hot-standby mode revertive wtr 15
 mpls te backup ordinary best-effort
 mpls te reserved-for-binding
 mpls te commit
```

### 🔎 Explicação dos principais parâmetros

* **tunnel-protocol mpls te**
  Define o túnel como MPLS-TE.

* **destination 5.5.5.5**
  Destino final do túnel (Loopback do PE remoto).

* **mpls te path explicit-path PE1-P2**
  Usa o caminho explícito criado.

* **mpls te record-route**
  Registra o caminho real percorrido pelo túnel.

* **hot-standby mode revertive wtr 15**

  * Cria túnel de backup quente
  * Retorna ao primário após 15 segundos

* **reserved-for-binding**
  Reserva o túnel para políticas (L2VPN / L3VPN).

* **mpls te commit**
  Aplica efetivamente a configuração do túnel.

---

## 🧩 5️⃣ OSPF com suporte a MPLS-TE

```text
ospf 1 router-id 1.1.1.1
 import-route direct
 import-route static
 opaque-capability enable
 enable traffic-adjustment advertise
 area 0.0.0.0
  network 10.0.0.0 0.0.0.3
  network 10.0.0.12 0.0.0.3
  mpls-te enable
```

### 🔎 Explicação

* **opaque-capability enable**
  Permite LSAs tipo 10 (usados por MPLS-TE).

* **enable traffic-adjustment advertise**
  Anuncia atributos TE:

  * Banda
  * Métricas
  * Estado do link

* **mpls-te enable**
  Integra OSPF com MPLS-TE na área.

📌 Sem isso, o CSPF **não funciona corretamente**.

---

## 🎯 6️⃣ Política de túnel (Tunnel Policy)

```text
tunnel-policy PE1PE5
 tunnel binding destination 5.5.5.5 te Tunnel0/0/0
```

### 📌 Função

* Associa o destino **5.5.5.5** ao túnel TE.
* Usado por:

  * L2VPN (L2VC / VPLS)
  * L3VPN
  * Políticas de tráfego

---

## 🔗 7️⃣ Usando o túnel em uma L2VPN (L2VC)

```text
mpls l2vc 5.5.5.5 1 tunnel-policy PE1PE5
```

📌 O tráfego L2 passará **dentro do túnel MPLS-TE**, respeitando:

* Caminho explícito
* Prioridade
* Redundância

---

## 🔄 8️⃣ Criando túnel de backup (Explicit Path secundário)

### Criar caminho secundário

```text
explicit-path PE1-P2-BACKUP
 next hop 10.0.0.x
 next hop 10.0.0.x
```

### Associar ao túnel

```text
interface Tunnel0/0/0
 mpls te path explicit-path PE1-P2-BACKUP secondary
```

📌 O túnel secundário entra automaticamente se o primário falhar.

---

## 🔍 9️⃣ Comandos de verificação

```text
display mpls te tunnel brief
display mpls te tunnel verbose
tracert lsp te Tunnel 0/0/0
display ospf lsdb opaque-area
display mpls rsvp-te session
```

---

## ✅ Estado esperado

* **Tunnel State: UP**
* **Primary e Backup operacionais**
* **RSVP sinalizado**
* **Tráfego passando pelo caminho definido**
* **Failover rápido e controlado**