```markdown
# Community BGP — Documentação Completa (Huawei)

Esta documentação reúne, em um único texto, os conceitos de **Community BGP**, seus tipos, communities públicas mais comuns e um guia prático de **criação de community-filters, route-policies e aplicação de communities** em roteadores Huawei.

---

## 📘 O que é Community BGP

Community é um atributo opcional do BGP utilizado para **marcar rotas** e **sinalizar políticas** entre roteadores e sistemas autônomos (AS). Ela permite aplicar decisões de roteamento sem a necessidade de ACLs complexas ou múltiplas sessões BGP.

---

## 2️⃣ Tipos de Community

### 🔹 Well-Known Communities (comunidades conhecidas)

São reconhecidas por todos os roteadores BGP.

- **NO_EXPORT**  
  A rota não deve ser anunciada para outros AS (fora do AS local).

- **NO_ADVERTISE**  
  A rota não deve ser anunciada para nenhum outro roteador, nem dentro nem fora do AS.

- **LOCAL_PREF**  
  Usada internamente para influenciar a preferência de rotas dentro do AS.

---

### 🔹 Standard Communities (numéricas)

Formato:
```

ASN:VALOR

```

Exemplo:
```

65001:100

````

Onde:
- **65001** → ASN que marcou a rota  
- **100** → valor definido pela política

Uso comum:
- Sinalização de rotas preferenciais
- Classificação de clientes
- Controle de exportação/importação
- Engenharia de tráfego simples

---

### 🔹 Extended Communities

Permitem informações mais avançadas, como:
- VPNs
- MPLS L3VPN
- Direcionamento de tráfego específico

Exemplo de uso:
- Definir qual site de uma VPN deve receber determinada rota

---

## 🌐 Communities Públicas (Well-Known)

| Community               | Significado |
|------------------------|-------------|
| **NO_EXPORT**           | Não anunciar para outros AS |
| **NO_ADVERTISE**        | Não anunciar para ninguém |
| **NO_EXPORT_SUBCONFED** | Não anunciar fora da subconfederação BGP |
| **LOCAL_AS**            | Anunciar somente dentro do AS local |
| **INTERNET**            | Anunciar para a internet pública (em alguns ambientes) |

---

# 📄 Documentação Prática: Community Filters e Route-Policies (Huawei)

## 🎯 Objetivo

Criar filtros de community, aplicar communities às rotas e controlar a **exportação e importação de rotas BGP** usando route-policy.

---

## 1️⃣ Criação de Community Filters

### Comandos
```bash
[SEU-ISP] ip community-filter basic OP-01 permit 65004:1
[SEU-ISP] ip community-filter basic OP-02 permit 65004:2
````

### Explicação

* `ip community-filter basic OP-01` → Cria um filtro chamado **OP-01**
* `permit 65004:1` → Identifica rotas marcadas com essa community
* O comando `undo` remove uma entrada do filtro
* Se tentar remover um filtro inexistente, o sistema retornará erro

---

## 2️⃣ Criação de Route-Policies com Community Filters

### OP01-OUT

```bash
[SEU-ISP] route-policy OP01-OUT permit node 11
[SEU-ISP-route-policy] if-match community-filter OP-01
[SEU-ISP-route-policy] q
```

### OP02-OUT

```bash
[SEU-ISP] route-policy OP02-OUT permit node 11
[SEU-ISP-route-policy] if-match community-filter OP-02
[SEU-ISP-route-policy] q
```

### Explicação

* `route-policy <nome> permit node <nó>` → Cria a policy e define a ordem de avaliação
* `if-match community-filter <filtro>` → A rota só passa se tiver a community correspondente
* Rotas que casam com o filtro são permitidas
* `q` → Sai da configuração do node

---

## 3️⃣ Aplicação de Communities em Rotas (Importação)

### Comando

```bash
route-policy 65100-IN permit node 10
 apply community 65004:2 65004:1
```

### Explicação

* `apply community` → Marca a rota com uma ou mais communities
* `65004:2 65004:1` → Ambas as communities são aplicadas
* Muito usado para:

  * Classificação posterior
  * Controle de exportação
  * Integração com políticas de upstream/CDN

---

### Exemplo Completo com Filtro de Prefixo

```bash
route-policy 65100-IN permit node 10
 if-match ip-prefix AS-65100BL
 apply community 65004:2 65004:1
```

Explicação:

* `if-match ip-prefix AS-65100BL` → Seleciona rotas de um prefixo específico
* Todas as rotas que passam pelo node recebem as communities definidas

---

## 4️⃣ Boas Práticas e Observações

* **Ordem importa**: o número do node define a prioridade da policy
* **permit vs deny**:

  * `permit` → permite a rota
  * `deny` → bloqueia a rota
* **Validação sempre necessária**:

```bash
display ip community-filter
display route-policy
```

* **Uso combinado**:

  * *Community-filter* → identifica rotas
  * *Route-policy* → aplica ações (permitir, negar, aplicar community, manipular atributos)

---

📌 **Resumo**:
Communities simplificam políticas BGP, aumentam controle sobre anúncios e permitem decisões inteligentes de roteamento quando bem combinadas com route-policies e filtros.

```
```
