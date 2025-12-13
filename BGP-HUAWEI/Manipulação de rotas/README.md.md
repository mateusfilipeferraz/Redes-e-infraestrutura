````markdown
# Manipulação de Rotas BGP — Huawei

Este documento consolida **manipulação de rotas BGP** em roteadores Huawei, cobrindo **Local Preference**, **engenharia de tráfego de entrada (download)** e **políticas baseadas em prefixos e communities**, com exemplos práticos para provedores e clientes.

---

## 🧩 Objetivo

- Definir **Local Preference** padrão para rotas recebidas de um peer
- Influenciar **tráfego de entrada (Inbound / Download)**
- Manipular rotas via:
  - Local Preference
  - AS-PATH prepend
  - Anúncio de prefixos mais específicos
  - MED (quando aplicável)
  - Communities BGP

---

## 🔧 Manipulação de Local Preference (Tráfego de Saída)

Local Preference afeta **qual link você usa para sair** (egress).  
Quanto **maior o valor**, **mais preferida** a rota.

### Exemplo: aumentar Local Preference para um peer específico

```bash
route-policy AS-65005-IN permit node 100
 apply local-preference 120
````

🔹 Todas as rotas recebidas pelo peer **AS 65005** passam a ter **Local Preference = 120**
🔹 Valor padrão normalmente é **100**

---

## 📥 Manipulando a Entrada do Tráfego (Inbound)

### Princípios rápidos (resumão)

* **Inbound** = como *outros ASes* escolhem o caminho até **seus prefixos**
* Você **não controla diretamente**, apenas **influencia**
* Métodos do mais comum ao mais intrusivo:

1. **AS-PATH prepend**
2. **Anúncio de prefixos mais específicos**
3. **BGP MED** (normalmente apenas entre o mesmo AS)
4. **Communities para upstreams** (dependem de suporte do provedor)

---

## 🔁 Exemplo A — AS-PATH Prepend (Huawei VRP)

**Cenário**

* Seu ASN: **65000**
* Peer A: `198.51.100.2`
* Peer B: `192.0.2.2`

### Tornar o caminho pelo Peer B menos atrativo (3 prepends)

```bash
system-view
route-policy OUT-AS65000 permit node 10
 apply as-path prepend 65000 65000 65000 additive
quit
```

🔹 O prepend faz com que outros ASes vejam esse caminho como mais longo
🔹 Muito utilizado para balancear download entre provedores

---

# 📘 Engenharia de Tráfego via Prefixos Mais Específicos (Huawei)

## A — Para seus blocos (200.200.0.0/22 → dois /23)

### 🧭 Objetivo

Dividir um **/22** em dois **/23** e anunciar cada um preferencialmente a um provedor diferente, influenciando o **download**.

---

### Comandos Essenciais (prontos para colar)

```bash
ip ip-prefix meusblocos index 10 permit 200.200.0.0 22
ip ip-prefix meusblocos-01-23 index 11 permit 200.200.0.0 23
ip ip-prefix meusblocos-02-23 index 11 permit 200.200.2.0 23
ip ip-prefix fullroute index 10 permit 0.0.0.0 0 greater-equal 8 less-equal 24

ip community-filter basic OP-01 permit 65004:1
ip community-filter basic OP-02 permit 65004:2
```

---

### Route-Policies de Exportação

```bash
route-policy OP01-OUT permit node 9
 if-match ip-prefix meusblocos-01-23

route-policy OP01-OUT permit node 10
 if-match ip-prefix meusblocos

route-policy OP02-OUT permit node 90
 if-match ip-prefix meusblocos-02-23

route-policy OP02-OUT permit node 100
 if-match ip-prefix meusblocos
```

---

### Route-Policies de Importação (Full Route)

```bash
route-policy OP01-IN permit node 10
 if-match ip-prefix fullroute

route-policy OP02-IN permit node 100
 if-match ip-prefix fullroute
```

---

### 🔍 Explicação do Efeito

* **200.200.0.0/23** → anunciado preferencialmente ao **Provedor 1**
* **200.200.2.0/23** → anunciado preferencialmente ao **Provedor 2**
* **200.200.0.0/22** permanece como agregado
* Communities **65004:1 / 65004:2** são opcionais (dependem do upstream respeitar)

---

### Comandos de Verificação

```bash
display bgp ipv4 unicast peer 10.0.0.1 advertised-routes
display bgp ipv4 unicast peer 10.0.0.5 advertised-routes
display bgp ipv4 unicast routing-table | include 200.200
display route-policy OP01-OUT
display route-policy OP02-OUT
display ip ip-prefix
```

---

## B — Para Cliente (200.0.0.0/22)

### 🧭 Objetivo

Aplicar políticas de **import/export** para prefixos do cliente, marcando sub-blocos com **communities diferentes** para sinalização e controle.

---

### Comandos Essenciais

```bash
ip ip-prefix AS-65100BL-22 index 10 permit 200.0.0.0 22
ip ip-prefix AS-65100BL-1-23 index 10 permit 200.0.0.0 23
ip ip-prefix AS-65100BL-2-23 index 10 permit 200.0.2.0 23
```

---

### Route-Policy de Importação do Cliente

```bash
route-policy 65100-IN permit node 10
 if-match ip-prefix AS-65100BL-22
 apply community 65004:2 65004:1

route-policy 65100-IN permit node 80
 if-match ip-prefix AS-65100BL-2-23
 apply community 65004:2

route-policy 65100-IN permit node 90
 if-match ip-prefix AS-65100BL-1-23
 apply community 65004:1
```

---

### Route-Policy de Exportação (Full Route)

```bash
route-policy 65100-OUT permit node 10
 if-match ip-prefix fullroute
```

---

### 🔍 Explicação do Efeito

* Prefixos do cliente recebem **communities diferentes conforme o bloco**
* Permite:

  * Sinalizar preferências ao cliente
  * Controle avançado de engenharia de tráfego
  * Integração com upstreams/CDNs que respeitam communities

---

### Comandos de Verificação

```bash
display bgp ipv4 unicast peer 10.0.0.22 received-routes
display route-policy 65100-IN
display ip ip-prefix | include 200.0.0.0
display ip community-filter
```

---

## 📌 Resumo Final

* **Local Preference** → controla saída (egress)
* **AS-PATH prepend** → influencia entrada (download)
* **Prefixos específicos** → método mais eficiente para inbound
* **Communities** → controle fino e sinalização avançada
* **Route-policy** é o ponto central de decisão no Huawei VRP

Essa combinação permite **engenharia de tráfego precisa, previsível e escalável**.

```
```
