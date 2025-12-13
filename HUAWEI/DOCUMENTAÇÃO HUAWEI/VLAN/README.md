# VLAN no Huawei

Perfeito — abaixo um **passo a passo prático e direto**, com exemplos, para criar e aplicar VLANs em **switches Huawei (CE68xx / CE12xxx)**. O guia vai do básico (criação de VLAN, portas access/trunk) até verificação e cenários com roteadores.

---

## 1️⃣ Conceitos rápidos

* **VLAN**: separação em camada 2 por ID (ex.: VLAN 140).
* **Access port**: porta **sem tag (untagged)**, aplica PVID. Usada para PCs.
* **Trunk port**: porta que carrega **VLANs taggeadas (802.1Q)** entre switches/roteadores.
* **Vlanif**: interface lógica **L3** para atribuir IP à VLAN (gateway ou gerência).
* **portswitch / undo portswitch**: define se a porta é **L2 (switch)** ou **L3 (roteada)**.

---

## 2️⃣ Criar VLANs

```text
system-view
vlan batch 10 20 140
quit
```

---

## 3️⃣ Configurar porta ACCESS (PC)

Exemplo: colocar a **GE1/0/1** na **VLAN 140** como access.

```text
system-view
interface GigabitEthernet1/0/1
 port link-type access
 port default vlan 140
 undo shutdown
 quit
```

### Verificação

```text
display interface GigabitEthernet1/0/1
display vlan 140
```

---

## 4️⃣ Configurar porta TRUNK (uplink / roteador)

Exemplo: **GE1/0/0** como trunk permitindo a VLAN 140.

```text
system-view
interface GigabitEthernet1/0/0
 port link-type trunk
 port trunk allow-pass vlan 140
 port trunk pvid 1
 undo shutdown
 quit
```

> **Nota:** `port trunk pvid X` define a VLAN **untagged** na porta trunk. Normalmente o PVID é 1.

### Verificação

```text
display port trunk
display interface GigabitEthernet1/0/0
```

---

## 5️⃣ Criar Vlanif (gateway / IP de gerência)

Exemplo: IP de gerência na **VLAN 140**.

```text
system-view
interface Vlanif140
 ip address 192.168.140.1 255.255.255.0
 undo shutdown
 quit
```

No host (PC):

* IP: `192.168.140.2/24`
* Gateway: `192.168.140.1`

### Verificação

```text
display interface Vlanif140
display ip interface brief
```

---

## 6️⃣ Converter porta para L3 (porta roteada)

Use **apenas** se quiser uma porta **ponto-a-ponto**, sem VLAN.

```text
system-view
interface GigabitEthernet1/0/0
 undo portswitch
 ip address 10.10.10.1 255.255.255.252
 undo shutdown
 quit
```

---

# VLANs em Roteadores Huawei

Essa é uma dúvida muito comum em laboratório e em campo.

👉 **Resposta curta:**

> ✅ O ideal é usar um **switch** entre o roteador e o PC quando o PC **não suporta VLAN**.

Vamos entender os cenários 👇

---

## 🧩 1️⃣ PC **não suporta VLAN** (sem 802.1Q)

### Problema

Se o roteador envia pacotes **taggeados**, o PC não entende a tag VLAN e **descarta os pacotes**.

### ✅ Solução ideal — usar switch

```text
[Roteador Huawei] ── [Switch] ── [PC]
```

O switch:

* Entende VLANs
* Recebe **trunk** do roteador
* Entrega **access (sem tag)** para o PC

### 🔹 Configuração no roteador

```text
system-view
interface GigabitEthernet1/0/0.10
dot1q termination vid 10
ip address 192.168.10.1 255.255.255.0
quit
```

### 🔹 Configuração no switch

```text
# Porta para o roteador
interface GigabitEthernet0/0/1
 port link-type trunk
 port trunk allow-pass vlan 10

# Porta para o PC
interface GigabitEthernet0/0/2
 port link-type access
 port default vlan 10
```

### 🔹 Configuração no PC

* IP: `192.168.10.2`
* Máscara: `255.255.255.0`
* Gateway: `192.168.10.1`

✅ O PC recebe pacotes **sem tag**
✅ O roteador mantém o roteamento por VLAN

---

## 🧩 2️⃣ PC ligado direto ao roteador (sem VLAN)

Funciona, mas **não há separação por VLAN**.

### 🔹 No roteador

```text
interface GigabitEthernet1/0/0
 ip address 192.168.10.1 255.255.255.0
 undo shutdown
```

### 🔹 No PC

* IP: `192.168.10.2/24`
* Gateway: `192.168.10.1`

✅ Simples e funcional
🚫 Sem VLAN
🚫 Não permite múltiplas redes no mesmo link

---

## 🧭 Conclusão

| Situação                              | Melhor configuração                         |
| ------------------------------------- | ------------------------------------------- |
| PC não suporta VLAN                   | Usar switch entre o roteador e o PC ✅       |
| PC suporta VLAN (Linux, Intel PROSet) | Conectar direto com subinterface dot1q ⚙️   |
| Somente acesso simples                | Conexão direta sem VLAN (IP na interface) ⚡ |
