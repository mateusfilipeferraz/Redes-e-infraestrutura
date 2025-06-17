# Configurando DHCP Relay no MikroTik

---

## 🗺️ Topologia

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

