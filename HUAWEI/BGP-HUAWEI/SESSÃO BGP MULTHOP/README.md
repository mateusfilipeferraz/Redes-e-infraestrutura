````markdown
# SESSÃO BGP MULTIHOP — Huawei

Este documento descreve como configurar uma **sessão BGP EBGP Multihop** em roteadores Huawei, utilizada quando o vizinho BGP **não está diretamente conectado**.

---

## 🧠 Conceito Rápido

Por padrão, o **EBGP** forma sessão apenas com vizinhos **diretamente conectados**, pois o TTL padrão é **1**.  
Com **EBGP Multihop**, o TTL é aumentado, permitindo que a sessão atravesse **roteadores intermediários**.

---

## 🔧 Exemplo Prático

### 🗺️ Cenário

- **Roteador local (Huawei)**  
  - ASN: **65001**  
  - IP local (Loopback): **10.0.0.1**

- **Vizinho remoto**  
  - ASN: **65002**  
  - IP remoto (Loopback): **20.0.0.2**

- Os roteadores **não estão diretamente conectados** (há hops intermediários).

---

## ⚙️ Passos de Configuração (Roteador Local)

```bash
system-view
bgp 65001
 peer 20.0.0.2 as-number 65002
 peer 20.0.0.2 ebgp-max-hop 5
 peer 20.0.0.2 connect-interface LoopBack0
````

---

## 🧩 Explicação dos Comandos

| Comando                                     | Função                                         |
| ------------------------------------------- | ---------------------------------------------- |
| `bgp 65001`                                 | Entra na instância BGP do ASN local            |
| `peer 20.0.0.2 as-number 65002`             | Define o vizinho BGP remoto                    |
| `peer 20.0.0.2 ebgp-max-hop 5`              | Permite até **5 hops** (TTL = 5)               |
| `peer 20.0.0.2 connect-interface LoopBack0` | Usa a Loopback como IP de origem (boa prática) |

---

## 🧠 Dica Importante (Loopback nos Dois Lados)

Se ambos os lados usam **Loopback**, a configuração deve existir **também no roteador remoto**.

### ⚙️ Configuração no Roteador Remoto

```bash
system-view
bgp 65002
 peer 10.0.0.1 as-number 65001
 peer 10.0.0.1 ebgp-max-hop 5
 peer 10.0.0.1 connect-interface LoopBack0
```

🔹 Certifique-se de que **existe rota** para os IPs de loopback em ambos os lados, via:

* Rota estática
* OSPF
* IS-IS
* Outro IGP

Sem conectividade IP, o BGP **não sobe**, mesmo com multihop configurado.

---

## ✅ Verificação da Sessão

```bash
display bgp peer
```

### Estados Comuns

* **Established** → Sessão BGP ativa
* **Active / Idle** → Verificar:

  * Conectividade IP (ping/traceroute)
  * TTL configurado (`ebgp-max-hop`)
  * IP de origem (`connect-interface`)
  * Rotas para Loopback

---
