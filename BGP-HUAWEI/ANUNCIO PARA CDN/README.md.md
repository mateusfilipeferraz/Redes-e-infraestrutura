````markdown
# ANÚNCIO PARA CDN — ANÚNCIO DO BLOCO DO PROVEDOR

## 🧭 Objetivo
Permitir anunciar **somente prefixos específicos (/24)** ou **prefixos marcados com a community de CDN (65004:10)** para o peer CDN (**AS 8888**), bloqueando qualquer importação de rotas indesejadas.

---

## ⚙️ Configuração Huawei — CDN (AS 8888)

### 1. Prefix-list (define os blocos que podem ser anunciados ao CDN)
```bash
ip ip-prefix meusblocos-24 index 10 permit 200.200.0.0 22 greater-equal 24 less-equal 24
````

🔹 `meusblocos-24` seleciona sub-blocos **/24** dentro do seu **/22**, permitindo granularidade no anúncio de rotas ao CDN.

---

### 2. Community-filter (marca as rotas específicas para CDN)

```bash
ip community-filter basic ALL-CDNs permit 65004:10
```

🔹 A community **65004:10** deve ser aplicada às rotas que precisam ser exportadas especificamente para o CDN.

---

### 3. Route-policy de exportação (CDNs.OUT)

```bash
route-policy CDNs.OUT permit node 100
 if-match community-filter ALL-CDNs

route-policy CDNs.OUT permit node 110
 if-match ip-prefix meusblocos-24
```

🔹 **Node 100**: permite a exportação de rotas marcadas com a community **65004:10**
🔹 **Node 110**: permite a exportação dos prefixos definidos em `meusblocos-24`
🔹 Qualquer rota que não corresponda a essas regras **não será anunciada**

---

### 4. Route-policy de importação (CDN-IN)

```bash
route-policy CDN-IN deny node 10
```

🔹 Bloqueia **todas** as rotas recebidas do CDN, protegendo o roteador contra anúncios indevidos.

---

### 5. Associação das políticas ao peer CDN

```bash
bgp 65000
 ipv4-family unicast
  peer 200.200.1.2 as-number 8888
  peer 200.200.1.2 description CDN-PEER
  peer 200.200.1.2 enable
  peer 200.200.1.2 route-policy CDNs.OUT export
  peer 200.200.1.2 route-policy CDN-IN import
```

---

### 6. Rota nula (proteção do agregado)

```bash
ip route-static 200.200.0.0 255.255.252.0 NULL0
```

🔹 Garante que o agregado /22 exista na RIB e evita tráfego indevido para prefixos não anunciados.

---

## ✅ Resultado esperado

Após aplicar essas configurações:

* O roteador anunciará **apenas prefixos /24** ou **rotas marcadas com a community 65004:10** para o peer **200.200.1.2 (AS 8888)**
* **Nenhuma rota** será importada do CDN
* O comando abaixo exibirá somente os anúncios permitidos:

```bash
display bgp ipv4 unicast peer 200.200.1.2 advertised-routes
```

```
```
