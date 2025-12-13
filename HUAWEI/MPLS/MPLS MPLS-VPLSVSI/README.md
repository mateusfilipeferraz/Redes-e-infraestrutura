Perfeito 👍
Abaixo está o **conteúdo revisado, organizado e tecnicamente padronizado**, com **explicação + implementação correta de VSI/VPLS em Huawei**, incluindo **sessão LDP remota**, exatamente no padrão usado em backbone e ambiente NOC.

---

## 🧩 O que é VSI (Virtual Switch Instance)

O **VSI (Virtual Switch Instance)** é uma **instância de comutação Ethernet virtual**, que funciona como um **switch lógico dentro do roteador Huawei**.

Ele é utilizado em serviços **VPLS (Virtual Private LAN Service)**, que são **L2VPNs multiponto**, permitindo que várias LANs, em locais diferentes, se comportem como se estivessem **no mesmo switch Ethernet**, mesmo atravessando uma rede MPLS.

👉 **Em resumo**

* O **VSI é o switch virtual**
* O **VPLS é o serviço**
* O **MPLS é o transporte**

---

## 🧠 Funcionamento do VSI no VPLS

* Cada site do cliente conecta sua LAN a uma interface do roteador
* Essa interface é **associada ao VSI**
* O VSI troca quadros Ethernet com outros VSIs do mesmo serviço
* A troca ocorre através de **pseudowires (PW)** sinalizados por **LDP**
* MACs aprendidos são distribuídos entre os roteadores

---

## 🏗️ Cenário do exemplo

| Equipamento | Router-ID | Função |
| ----------- | --------- | ------ |
| R1          | 1.1.1.1   | PE     |
| R2          | 5.5.5.5   | PE     |
| R3          | 6.6.6.6   | PE     |

* Serviço: **VPLS**
* VSI: **AMARELO**
* VSI-ID: **10**
* Sinalização: **LDP**
* Tipo: **Static (full-mesh manual)**

---

## ⚙️ Configuração do VSI – R1

```text
[R1] vsi AMARELO static
[R1-vsi-AMARELO] pwsignal ldp
[R1-vsi-AMARELO-ldp] vsi-id 10
[R1-vsi-AMARELO-ldp] peer 5.5.5.5
[R1-vsi-AMARELO-ldp] peer 6.6.6.6
```

---

## ⚙️ Configuração do VSI – R2

```text
[R2] vsi AMARELO static
[R2-vsi-AMARELO] pwsignal ldp
[R2-vsi-AMARELO-ldp] vsi-id 10
[R2-vsi-AMARELO-ldp] peer 1.1.1.1
[R2-vsi-AMARELO-ldp] peer 6.6.6.6
```

---

## ⚙️ Configuração do VSI – R3

```text
[R3] vsi AMARELO static
[R3-vsi-AMARELO] pwsignal ldp
[R3-vsi-AMARELO-ldp] vsi-id 10
[R3-vsi-AMARELO-ldp] peer 1.1.1.1
[R3-vsi-AMARELO-ldp] peer 5.5.5.5
```

📌 **Observação importante**
Em VSI `static`, é necessário **full-mesh manual**, ou seja:

* Cada PE deve apontar para **todos os outros PEs**

---

## 🔌 Associar interface física ao VSI (lado do cliente)

Exemplo no **R1**:

```text
[R1] interface GigabitEthernet1/0/0
[R1-GigabitEthernet1/0/0] l2 binding vsi AMARELO
```

📌 Essa interface passa a:

* Operar em **Layer 2**
* Encaminhar quadros Ethernet para o VSI
* Não usa IP

---

## 🔗 Sessão LDP remota (obrigatória no VPLS)

Para que os **pseudowires funcionem**, é necessário criar sessões **LDP remotas** entre os PEs.

### Exemplo no R1

```text
[R1] mpls ldp
[R1-mpls-ldp] remote-peer R2
[R1-mpls-ldp-remote-R2] remote-ip 5.5.5.5
quit

[R1] mpls ldp
[R1-mpls-ldp] remote-peer R3
[R1-mpls-ldp-remote-R3] remote-ip 6.6.6.6
quit
```

(O mesmo deve ser feito nos outros roteadores, apontando para os peers correspondentes.)

---

## 🔍 Comandos de verificação

```text
display vsi
display vsi name AMARELO
display vsi peer
display l2vpn connection
display mpls ldp peer
display mac-address vsi AMARELO
```

---

## 📌 Diferença rápida: VSI × L2VC

| Característica  | VSI (VPLS)    | L2VC              |
| --------------- | ------------- | ----------------- |
| Tipo            | Multiponto    | Ponto-a-ponto     |
| Aprendizado MAC | Sim           | Limitado          |
| Escalabilidade  | Média         | Alta              |
| Uso comum       | LAN estendida | Circuito dedicado |

