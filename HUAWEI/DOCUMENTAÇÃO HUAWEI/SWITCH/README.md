# Switches Huawei — Guia de Configuração e Diagnóstico

Este guia reúne os **principais comandos de configuração** e **diagnóstico** para switches Huawei, organizado por tópicos para consulta rápida (estilo *cheat sheet*).

---

## 1️⃣ Configuração Básica e Login

### Login via console

```text
Login authentication
Username: admin1
Password:
```

### Entrar em modo de configuração

```text
<HUAWEI> system-view
```

### Definir hostname

```text
[HUAWEI] sysname NOME_DO_DISPOSITIVO
```

---

## 2️⃣ Configuração de VLANs

### Criar VLANs

```text
[HUAWEI] vlan batch 10 20 100
```

### Definir VLAN de gerenciamento

```text
[HUAWEI] vlan 5
[HUAWEI-VLAN5] management-vlan
```

### Criar interface VLANIF

```text
[HUAWEI] interface vlanif 10
[HUAWEI-Vlanif10] ip address 10.10.10.1 24
```

---

## 3️⃣ Configuração de Portas

### Porta ACCESS

```text
[HUAWEI] interface GigabitEthernet1/0/1
[HUAWEI-GE1/0/1] port link-type access
[HUAWEI-GE1/0/1] port default vlan 10
```

### Porta TRUNK

```text
[HUAWEI] interface 10GE1/0/1
[HUAWEI-10GE1/0/1] port link-type trunk
[HUAWEI-10GE1/0/1] port trunk allow-pass vlan 10 20
```

### STP

```text
# Porta de borda (edge)
[HUAWEI-GE1/0/1] stp edged-port enable

# Desativar STP na interface
[HUAWEI-GE1/0/1] stp disable
```

---

## 4️⃣ Eth-Trunk (Agregação de Links)

### Criar Eth-Trunk

```text
[HUAWEI] interface eth-trunk 1
[HUAWEI-Eth-Trunk1] port link-type trunk
[HUAWEI-Eth-Trunk1] port trunk allow-pass vlan 10
[HUAWEI-Eth-Trunk1] mode lacp-static
```

### Adicionar interface ao Eth-Trunk

```text
[HUAWEI] interface GigabitEthernet1/0/1
[HUAWEI-GE1/0/1] eth-trunk 1
```

---

## 5️⃣ DHCP

### Ativar DHCP

```text
[HUAWEI] dhcp enable
```

### Criar pool DHCP

```text
[HUAWEI] ip pool 10
[HUAWEI-ip-pool-10] network 10.10.10.0 mask 24
[HUAWEI-ip-pool-10] gateway-list 10.10.10.1
[HUAWEI-ip-pool-10] dns-list 8.8.8.8
```

### IP fixo por MAC

```text
[HUAWEI-ip-pool-10] static-bind ip-address 10.10.10.254 mac-address a-b-c
```

### Usar pool na VLAN

```text
[HUAWEI] interface vlanif 10
[HUAWEI-Vlanif10] dhcp select global
```

---

## 6️⃣ Roteamento

### Rota estática padrão

```text
[HUAWEI] ip route-static 0.0.0.0 0.0.0.0 10.10.100.2
```

### OSPF

```text
[HUAWEI] ospf 100 router-id 2.2.2.2
[HUAWEI-ospf-100] area 0
[HUAWEI-ospf-100-area-0.0.0.0] network 172.16.1.0 0.0.0.255
```

---

## 7️⃣ VRRP

```text
[HUAWEI] interface vlanif 10
[HUAWEI-Vlanif10] vrrp vrid 1 virtual-ip 192.168.10.3
[HUAWEI-Vlanif10] vrrp vrid 1 priority 120
[HUAWEI-Vlanif10] vrrp vrid 1 preempt-mode timer delay 20
```

### Monitorar interface no VRRP

```text
[HUAWEI-Vlanif10] vrrp vrid 1 track interface 10GE1/0/7 reduced 100
```

---

## 8️⃣ Segurança (DHCP Snooping + IPSG)

### DHCP Snooping

```text
[HUAWEI] dhcp snooping enable
```

### Interface confiável

```text
[HUAWEI] interface eth-trunk 1
[HUAWEI-Eth-Trunk1] dhcp snooping trusted
```

### IPSG na VLAN

```text
[HUAWEI] vlan 10
[HUAWEI-vlan10] ipv4 source check user-bind enable
```

---

## 9️⃣ NAT (Roteadores Huawei)

### NAT outbound

```text
[Router] interface GigabitEthernet0/0/1
[Router-GigabitEthernet0/0/1] nat outbound 2000
```

### NAT server (port forwarding)

```text
[Router] interface GigabitEthernet0/0/0
[Router-GigabitEthernet0/0/0] nat server protocol tcp global current-interface www inside 192.168.50.20 www
```

---

## 🔟 QoS (Limitação de Taxa)

### Limitar por IP

```text
[Router] interface GigabitEthernet0/0/1
[Router-GigabitEthernet0/0/1] qos car inbound source-ip-address range 192.168.10.1 to 192.168.10.254 per-address cir 512
```

### Limitar por ACL

```text
[Router] qos car inbound acl 2222 cir 2048
```

---

## 1️⃣1️⃣ Salvamento da Configuração

```text
<HUAWEI> save
```

---

## 1️⃣2️⃣ Verificações Comuns

```text
# VLANs
display vlan

# Eth-Trunk
display eth-trunk 1

# Roteamento
display ip routing-table

# Pool DHCP
display ip pool name 10
```

---

# 🔎 Diagnóstico de Portas e Transceivers

## Diagnóstico de Transceiver (SFP / Óptico)

```text
# Informações básicas
display transceiver interface XGigabitEthernet0/0/27

# Diagnóstico detalhado
display transceiver diagnosis interface XGigabitEthernet0/0/27

# Todos os transceivers
display transceiver
```

---

## 📡 Diagnóstico de Porta / Link

```text
# Status detalhado da interface
display interface XGigabitEthernet0/0/27

# Resumo geral das portas
display interface brief

# Teste de cabo (Ethernet)
diagnose interface XGigabitEthernet0/0/27
```

---

## 🧪 Diagnóstico de Fibra

```text
# Loopback
loopback internal interface XGigabitEthernet0/0/27
loopback external interface XGigabitEthernet0/0/27

# Diagnóstico físico
display interface phy-diagnosis XGigabitEthernet0/0/27
```

---

## ⚡ Outros comandos úteis

```text
# Alarmes
display alarm all

# Logs do sistema
display logbuffer
```

---

## 🧭 Resumo rápido

* **Sinal óptico** → `display transceiver`
* **Status da porta** → `display interface`
* **Erros físicos** → `display interface phy-diagnosis`
* **Problema em cabo cobre** → `diagnose interface`
