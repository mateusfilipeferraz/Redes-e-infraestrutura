````markdown
# IP TRÂNSITO — BGP (Huawei)

Este cenário considera que **os passos iniciais seguem a documentação base** (criação de BGP, peers, filtros básicos e sessão estabelecida). Abaixo estão **apenas os passos necessários para receber os prefixos do cliente e repassá-los adiante**, além de entregar a **full route** ao cliente.

---

## 📥 Receber prefixos do cliente e anunciar para frente

Neste ambiente, os **ip-prefix já existentes** são reutilizados, não sendo necessária a recriação dos filtros de full route.

---

## 📤 Enviar Full Route para o Cliente

### Criar a route-policy de exportação (Full Route)

Entrar no modo de configuração da route-policy:
```bash
route-policy 65100-OUT permit node 10
````

Definir as condições de correspondência (filtro de full route):

```bash
if-match ip-prefix fullroute
```

🔹 Essa policy permite que a **tabela full route** seja anunciada ao cliente.

---

## 📥 Receber os Blocos do Cliente

### Criar o IP Prefix dos blocos do cliente

```bash
ip ip-prefix AS-65100BL index 10 permit 200.0.0.0 22 greater-equal 22 less-equal 24
```

🔹 Permite receber anúncios do cliente entre **/22 e /24**.

---

### Criar a route-policy de importação

```bash
route-policy 65100-IN permit node 10
 if-match ip-prefix AS-65100BL
```

🔹 Apenas os prefixos do cliente que correspondam ao filtro serão aceitos.

---

## 🔗 Aplicar as policies no PEER BGP

```bash
peer 10.0.0.22 route-policy 65100-IN import
peer 10.0.0.22 route-policy 65100-OUT export
```

🔹 `import` → controla o que é **recebido do cliente**
🔹 `export` → controla o que é **enviado ao cliente** (full route)

---

## ✅ Resultado Esperado

* O roteador **recebe apenas os prefixos autorizados do cliente**
* Esses prefixos podem ser **repassados para outros peers/upstreams**
* O cliente recebe a **full route** conforme definido na policy
* Maior controle e segurança no trânsito IP

---

📌 **Boas práticas**:

* Sempre valide com:

```bash
display bgp peer
display bgp routing-table peer 10.0.0.22 received-routes
display bgp routing-table peer 10.0.0.22 advertised-routes
```

* Evite importar prefixos amplos sem filtros (`0.0.0.0/0`) de clientes
* Utilize communities para controle avançado de exportação quando necessário

```
```
