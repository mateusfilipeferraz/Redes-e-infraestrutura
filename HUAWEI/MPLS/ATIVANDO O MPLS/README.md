Perfeito 👍
Abaixo está **todo o conteúdo organizado e padronizado em Markdown**, como um **guia completo de ativação do MPLS + L2VPN (L2VC) em Huawei**, do zero até as verificações.

---

# 🟣 Ativando MPLS e L2VPN (L2VC) em Roteadores Huawei

Este guia considera que **links ponto-a-ponto (PTP)** e **Loopbacks** já foram criados.
O **OSPF** é usado como IGP para facilitar a troca de rotas entre os roteadores do core MPLS.

---

## 🧭 Pré-requisitos

* Interfaces PTP configuradas e funcionais
* Loopback configurada (recomendada para LSR-ID)
* OSPF ativo e com vizinhança **FULL** entre os roteadores
* Conectividade IP entre as loopbacks dos PEs

---

## 🧠 Topologia (conceito)

* MPLS ativo no **core**
* LDP ativo em todos os links MPLS
* L2VPN tipo **L2VC (ponto a ponto)**
* Sinalização via **LDP remoto**

---

## ⚙️ 1️⃣ Ativando MPLS no roteador

```text
system-view
mpls lsr-id 10.5.5.1 loopback
mpls
mpls ldp
mpls l2vpn
```

### 🔎 Explicação

* `mpls lsr-id` → define o identificador MPLS (use Loopback)
* `mpls` → habilita MPLS globalmente
* `mpls ldp` → habilita o protocolo de distribuição de labels
* `mpls l2vpn` → habilita serviços L2VPN

---

## 🔌 2️⃣ Ativando MPLS nas interfaces do core

> **Este passo deve ser feito em TODAS as interfaces que fazem parte do backbone MPLS**

```text
interface GigabitEthernet1/0/1
 mpls
 mpls ldp
```

📌 Repita **em todos os roteadores e interfaces MPLS**, dos dois lados.

---

## 🔁 3️⃣ Configuração completa (exemplo com L2VPN L2VC)

### 📍 No PE (R1)

```text
system-view

mpls lsr-id 10.5.5.1
mpls
mpls ldp
mpls l2vpn

interface GigabitEthernet1/0/0
 mpls
 mpls ldp
```

---

## 🌐 4️⃣ Criando peer LDP remoto (PE remoto – R5)

```text
mpls ldp remote-peer R5
 remote-ip 10.5.5.5
```

📌 Necessário quando os PEs **não são vizinhos diretos** no core MPLS.

---

## 🧱 5️⃣ Criando o template de pseudowire (PW)

```text
pw-template pw-pe R5
 peer-address 10.5.5.5
 control-word
 mtu 2000
```

### 🔎 Boas práticas

* **control-word** → evita problemas de ordenação
* **mtu 2000** → previne fragmentação em MPLS

---

## 🔗 6️⃣ Criando o circuito L2VPN (L2VC)

Na interface que conecta ao **cliente**:

```text
interface GigabitEthernet1/0/2
 mpls l2vc pw-template R5 64001
```

### Onde:

* `R5` → nome do pw-template
* `64001` → VC-ID (**deve ser igual no outro PE**)

📌 A interface passa a operar em **Layer 2 puro**.

---

## 🔄 7️⃣ Configuração no outro PE (R5)

Deve conter:

* MPLS + LDP ativos
* LDP remote-peer apontando para R1
* Mesmo **VC-ID**
* Interface cliente associada

Exemplo:

```text
mpls lsr-id 10.5.5.5
mpls
mpls ldp
mpls l2vpn

mpls ldp remote-peer R1
 remote-ip 10.5.5.1

interface GigabitEthernet1/0/2
 mpls l2vc pw-template R1 64001
```

---

## 🔍 8️⃣ Comandos de verificação

### Sessões LDP

```text
display mpls ldp peer
display mpls ldp remote-peer
display mpls ldp session
```

### Estado do L2VC

```text
display mpls l2vc
display mpls l2vc interface
display mpls l2vc verbose
```

### Roteamento (IGP)

```text
display ip routing-table
display ospf peer
```

---

## ✅ Estado esperado

* **LDP Session: Operational**
* **VC State: UP**
* **Labels distribuídos corretamente**
* Tráfego L2 passando transparente entre os sites
