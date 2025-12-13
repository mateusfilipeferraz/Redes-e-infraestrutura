![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/VPN-Site%20to%20cliente%20-%20Mikrotik/Screenshot_1.png)

# VPN OpenVPN - Site to Client (Mikrotik)
Download do pdf completo com ilustração. https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/VPN-Site%20to%20cliente%20-%20Mikrotik/Vpn%20OpenVpn%20site%20to%20client.pdf

Guia de configuração para estabelecer uma conexão VPN do tipo "site to client" utilizando OpenVPN em um roteador Mikrotik.

## 📌 Pré-requisitos

- Roteador Mikrotik com IP público
- Winbox ou acesso via terminal
- Pacote OpenVPN instalado no Mikrotik
- Certificados configurados
- Firewall devidamente ajustado

---

## 🛠️ Etapas de Configuração (Matriz)

### 1. Criar Interface de Loopback
```shell
/interface bridge add name=loopback
/ip address add address=10.10.1.1/24 interface=loopback
````

### 2. Criar Pool de IPs para VPN

```shell
/ip pool add name=openvpn ranges=10.10.1.3-10.10.1.254
```

### 3. Criar Perfil PPP

```shell
/ppp profile add name=openvpn local-address=10.10.1.2 remote-address=openvpn change-tcp-mss=yes use-compression=yes
```

### 4. Criar Secret para o Cliente

```shell
/ppp secret add name=VPN-SECRET password=SENHA123!Mikrotik! profile=openvpn service=ovpn
```

### 5. Habilitar o Servidor OpenVPN

```shell
/interface ovpn-server server set certificate=SERVER cipher=aes256-cbc,blowfish128,aes128-cbc auth=sha1 default-profile=openvpn enabled=yes port=1194
```

---

## 🔐 Geração de Certificados

### Criação

```shell
/certificate add name=ca-template common-name=CA_R1 key-usage=key-cert-sign,crl-sign
/certificate add name=server-template common-name=SERVER key-usage=tls-server
/certificate add name=client-R2-template common-name=client-R2 key-usage=tls-client
```

### Assinatura

```shell
/certificate sign ca-template ca-crl-host=IP_Público name=CA_R1
/certificate sign ca=CA_R1 server-template name=SERVER
/certificate sign ca=CA_R1 client-R2-template name=client-R2
```

### Exportação

```shell
/certificate export-certificate CA_R1
/certificate export-certificate client-R2 export-passphrase=SENHA123!
```

---

## 🔥 Regras de Firewall

```shell
/ip firewall address-list add list=openvpn address=10.10.1.0/24 comment="Endereços da rede OpenVPN"

/ip firewall nat add chain=srcnat action=masquerade src-address-list=openvpn out-interface=ether1 comment="Masquerade para clientes VPN"

/ip firewall filter add chain=input dst-port=1194 protocol=tcp comment="Permitir entrada OpenVPN"
```

---

## 🌐 Acesso à Internet via VPN

Se desejar que clientes conectados via VPN acessem a internet utilizando a saída da matriz, certifique-se de configurar o NAT conforme acima.

---

## ⚙️ Ajustes no Cliente OpenVPN

No arquivo de configuração `.ovpn` do cliente:

### Remover:

```
ping-timer-rem
```

### Adicionar:

```shell
remote-cert-tls server
route 10.10.2.0 255.255.255.0
route 10.10.1.1 255.255.255.0
```

---

## 👤 Autor

**Mateus Filipe Ferraz**
Documentação original em Confluence

```


