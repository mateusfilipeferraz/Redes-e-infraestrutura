# DHCP em Equipamentos Huawei — Cliente e Servidor

Este guia mostra **como configurar DHCP em equipamentos Huawei**, cobrindo:

* Cliente DHCP (interface recebe IP automaticamente)
* Servidor DHCP
* DHCP em VLAN (subinterface dot1q / Vlanif)

---

## 1️⃣ Ativar cliente DHCP (receber IP via DHCP)

Usado quando a interface deve **obter IP automaticamente** de outro servidor DHCP.

```text
system-view
interface GigabitEthernet1/0/0
 ip address dhcp-alloc
 dhcp client enable
quit
```

📌 Uso comum:

* Interface WAN
* Links de laboratório
* Ambientes onde o IP não é fixo

---

## 2️⃣ Criar um servidor DHCP

### 🔹 Habilitar o serviço DHCP globalmente

```text
system-view
dhcp enable
```

---

### 🔹 Criar pool de endereços DHCP

```text
ip pool DHCP-LAN
 gateway-list 192.168.2.1
 network 192.168.2.0 mask 255.255.255.0
 section 0 192.168.2.2 192.168.2.254
 dns-list 8.8.8.8 8.8.4.4
 excluded-ip-address 192.168.2.100 192.168.2.110
 lease day 1 hour 12 minute 30
quit
```

---

### 🔹 Associar o pool a uma interface

```text
interface Vlanif10
 ip address 192.168.2.1 255.255.255.0
 dhcp select global
quit
```

---

## ✅ Explicações importantes

* `system-view` é sempre obrigatório antes das configurações.
* O **gateway padrão** é definido dentro do `ip pool`.
* O comando `network` é **obrigatório** no Huawei para DHCP funcionar.
* `dns-list` aceita **múltiplos servidores DNS**.
* `excluded-ip-address` pode ser:

  * Intervalo → `excluded-ip-address X Y`
  * IP único → `excluded-ip-address X`
* O pool DHCP **sempre precisa estar associado** a uma interface L3 (Vlanif ou subinterface).

---

## 3️⃣ DHCP em VLAN (subinterface dot1q)

Cenário típico:

* VLAN 10
* Gateway no roteador
* DHCP fornecido pelo próprio roteador

---

### 🔹 Configuração completa

```text
system-view

# Ativar DHCP global
dhcp enable

# Criar pool para a VLAN 10
ip pool VLAN10
 gateway-list 192.168.10.1
 network 192.168.10.0 mask 255.255.255.0
 dns-list 8.8.8.8 1.1.1.1
 excluded-ip-address 192.168.10.1 192.168.10.10
quit

# Subinterface VLAN 10
interface GigabitEthernet1/0/0.10
 dot1q termination vid 10
 ip address 192.168.10.1 255.255.255.0
 dhcp select global
quit

save
```

---

## 🧭 Resumo rápido

| Situação      | Comando-chave                |
| ------------- | ---------------------------- |
| Cliente DHCP  | `ip address dhcp-alloc`      |
| Ativar DHCP   | `dhcp enable`                |
| Criar pool    | `ip pool NOME`               |
| Definir rede  | `network X mask Y`           |
| Associar pool | `dhcp select global`         |
| DHCP em VLAN  | Subinterface dot1q ou Vlanif |
