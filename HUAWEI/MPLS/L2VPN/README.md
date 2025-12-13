Perfeito 👍
Abaixo está a **explicação clara + configuração organizada de L2VPN (L2VC) em Huawei**, cobrindo:

* Peer LDP remoto
* L2VC **sem pw-template**
* L2VC **com pw-template (opcional e recomendado)**
* Associação na interface do cliente
* Verificação e troubleshooting

Tudo no padrão usado em **backbone/MPLS de operadora**.

---

# 🧩 O que é L2VPN (L2VC)

**L2VPN (Layer 2 VPN)** permite estender um **link Ethernet ponto-a-ponto** através de uma rede MPLS.

👉 Diferente do VPLS (VSI):

* **L2VC = ponto a ponto**
* **VPLS/VSI = multiponto**

---

## 🧠 Componentes principais

* **LDP remoto** → cria a sessão de sinalização
* **VC-ID** → identifica o circuito (deve ser igual nos dois lados)
* **Pseudowire (PW)** → o “cabo virtual”
* **Interface cliente** → porta L2 associada ao PW

---

# 🔗 1️⃣ Criando peer LDP remoto (obrigatório)

Antes de qualquer L2VPN, o **LDP remoto precisa estar UP**.

### Exemplo

```text
mpls ldp remote-peer R1
 remote-ip 10.5.5.1
```

📌 Isso cria uma sessão LDP **direta entre os PEs**, mesmo que não sejam vizinhos físicos.

---

# ⚙️ 2️⃣ L2VC simples (SEM pw-template)

Forma mais direta e muito usada em laboratório.

```text
interface GigabitEthernet1/0/0
 mpls l2vc 10.5.5.5 100
```

### Onde:

* `10.5.5.5` → IP do PE remoto
* `100` → VC-ID (TEM que ser igual no outro lado)

✅ Rápido
🚫 Pouca padronização
🚫 Difícil manter em ambientes grandes

---

# 🧱 3️⃣ Criando um PW-TEMPLATE (opcional, mas recomendado)

O **pw-template** permite padronizar MTU, control-word, etc.

```text
pw-template pw-pe R1
 peer-address 10.5.5.1
 control-word
 mtu 2000
```

### Explicando:

* **peer-address** → IP do PE remoto
* **control-word** → evita problemas de ordenação e interoperabilidade
* **mtu 2000** → evita fragmentação (muito importante)

📌 Em operadoras, **pw-template é padrão obrigatório**.

---

# 🔌 4️⃣ Criando a L2VPN na interface do cliente

Agora associamos o pseudowire à porta física.

```text
interface GigabitEthernet1/0/0
 mpls l2vc pw-template R1 64001
```

### Onde:

* `R1` → nome do pw-template
* `64001` → VC-ID (igual no outro PE)

📌 A interface passa a ser **Layer 2 pura**, sem IP.

---

# 🧭 5️⃣ O que precisa existir no outro lado (PE remoto)

No roteador remoto:

* Mesmo **VC-ID**
* Peer apontando de volta
* Interface cliente associada ao mesmo circuito

Exemplo:

```text
mpls ldp remote-peer R2
 remote-ip 10.5.5.2

interface GigabitEthernet1/0/0
 mpls l2vc pw-template R2 64001
```

---

# 🔍 6️⃣ Comandos de verificação

### Sessões LDP

```text
display mpls ldp session
display mpls ldp peer
```

### Estado da L2VPN

```text
display mpls l2vc
display mpls l2vc interface
```

### Ver detalhes do PW

```text
display mpls l2vc verbose
```

