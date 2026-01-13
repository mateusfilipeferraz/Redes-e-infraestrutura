# Configurando DHCP Relay no MikroTik

---

## 🗺️ Topologia

<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/MIKROTIK/Dhcp%20relay%20Mikrotik/1.png"  width="600" />

### Primeiro passo:

Coloque os IPs nas interfaces:

| Dispositivo | Interface | IP               | Máscara   |
|-------------|-----------|------------------|-----------|
| Serv-dhcp   | ether2    | 192.168.1.1      | /24       |
| Relay       | ether1    | 192.168.1.2      | /24       |
| Relay       | ether2    | 192.168.40.1     | (sem máscara especificada) |

---

## 🔄 Teste de comunicação e rotas

- Faça o teste de **ping** para verificar se o relay se comunica com o servidor.
- Coloque as rotas necessárias conforme abaixo:

### No servidor (Serve)

| Tipo de rota | Destino           | Gateway/Interface |
|--------------|-------------------|-------------------|
| DAc          | 192.168.1.0/24    | ether2            |
| As           | 192.168.40.0/24   | 192.168.1.2       |

### No relay

| Tipo de rota | Destino           | Gateway/Interface |
|--------------|-------------------|-------------------|
| As           | 0.0.0.0/0         | 192.168.1.1       |
| DAc          | 192.168.1.0/24    | ether1            |
| DAc          | 192.168.40.0/24   | ether2            |

---

## ⚙️ Configurando o servidor DHCP

### 1. Configurar o pool de IPs
ip>pool
<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Dhcp%20relay%20Mikrotik/2.png"  width="600" />


### 2. Configurar as networks
ip > dhcp-server> networks
<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Dhcp%20relay%20Mikrotik/3.png"  width="600" />

### 3. Configurar o DHCP server
ip > dhcp-server > dhcp
<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Dhcp%20relay%20Mikrotik/4.png"  width="600" />

---

## 📡 Configurando o DHCP Relay
ip > dhcp-relay
<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Dhcp%20relay%20Mikrotik/5.png"  width="600" />


---

## ✅ Concluído!

Após essas configurações, o DHCP relay já deve estar funcionando corretamente.
<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Dhcp%20relay%20Mikrotik/7.png"  width="600" />
---
