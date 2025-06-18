

# Configuração VPN OVPN - Roteadores MikroTik

Este script fornece a configuração completa para estabelecer uma VPN OVPN entre dois roteadores MikroTik: R1 como servidor (Matriz) e R2 como cliente (Filial). Ele inclui a geração de certificados, configurações PPP, do servidor OVPN e do cliente, além das regras de firewall necessárias.

**Atenção:**
* **Substitua os valores placeholder** como `SEU_IP_PUBLICO_AQUI`, `ip_local`, `ip_remoto`, `IP_WAN_DO_R1` pelas suas informações reais.
* A **transferência manual dos certificados do R1 para o R2 é um passo crucial** e não pode ser automatizada por este script.

```mikrotik
# --- Variáveis para R1 (Matriz) ---
:global caPublicIP "SEU_IP_PUBLICO_AQUI";
:global vpnPassword "SENHA123!Mikrotik!";
:global clientExportPassword "SENHA123!";
:global r1LocalAddress "ip_local";
:global r2RemoteAddress "ip_remoto";
:global ovpnPort "1194";

# --- 1. Geração e Assinatura de Certificados (R1) ---
/certificate add name=ca-template common-name=CA_R1 key-usage=key-cert-sign,crl-sign;
/certificate add name=server-template common-name=SERVER;
/certificate add name=client-R2-template common-name=client-R2;
/certificate sign ca-template ca-crl-host=$caPublicIP name=CA_R1;
/certificate sign ca=CA_R1 server-template name=SERVER;
/certificate sign ca=CA_R1 client-R2-template name=client-R2;
/certificate set CA_R1 trusted=yes;
/certificate set SERVER trusted=yes;

# --- 2. Exportação de Certificados (R1 - Necessário Transferir para R2) ---
# Estes comandos exportarão os certificados para o sistema de arquivos do roteador.
# Você precisará transferir manualmente estes arquivos (via FTP, SFTP, menu de arquivos do WinBox)
# do R1 para o R2 antes de executar o script do R2.
/certificate export-certificate CA_R1;
/certificate export-certificate client-R2 export-passphrase=$clientExportPassword;

# --- 3. Perfil PPP e Secret (R1) ---
/ppp profile add name=openvpn local-address=$r1LocalAddress remote-address=$r2RemoteAddress change-tcp-mss=yes use-compression=no use-encryption=required;
/ppp secret add name=VPN-SECRET password=$vpnPassword profile=openvpn service=ovpn;

# --- 4. Configuração do Servidor OVPN (R1) ---
/interface ovpn-server server set certificate=SERVER cipher=aes256-cbc auth=sha1 default-profile=openvpn enabled=yes require-client-certificate=no;

# --- 5. Regra de Firewall para o Servidor OVPN (R1) ---
/ip firewall filter add chain=input dst-port=$ovpnPort protocol=tcp action=accept comment="Permitir conexoes OVPN";

:delay 5s; # Dá um tempo para os comandos serem aplicados se executando diretamente
:log info "Configuracao do Servidor OVPN no R1 concluida. Lembre-se de transferir os certificados para o R2.";


# --- Variáveis para R2 (Filial) ---
# Senha da interface-opvn mesma senha do R1
:global vpnPassword "SENHA123!Mikrotik!";
# Senha para importar certificado mesma senha do R1
:global clientExportPassword "SENHA123!";
:global r1WanIP "IP_WAN_DO_R1"; # IP WAN do R1 que o cliente OVPN se conectará
:global ovpnPort "1194";
# Só mexa se tiver trocado o nome dos certificados no R1
:global clientCertName "cert_export_client-R2.crt_0"; # Verifique o nome exato do certificado importado

# --- 1. Importação de Certificados (R2 - Requer Transferência Manual do R1) ---
# Certifique-se de que estes arquivos (cert_export_CA_R1.crt, cert_export_client-R2.crt, cert_export_client-R2.key)
# já estão no sistema de arquivos do R2.
/certificate import file-name=cert_export_CA_R1.crt passphrase="";
/certificate import file-name=cert_export_client-R2.crt passphrase=$clientExportPassword;
/certificate import file-name=cert_export_client-R2.key passphrase=$clientExportPassword;

# --- 2. OVPN Client Configuration (R2) ---
# Agora usando o nome exato do certificado conforme ele é importado.
/interface ovpn-client add certificate=$clientCertName cipher=aes256-cbc auth=sha1 connect-to=$r1WanIP name=ovpn-R2 password=$vpnPassword profile=default-encryption user=VPN-SECRET;

# --- 3. Regra de Firewall para o Cliente OVPN (R2 - Geralmente não é necessária para um cliente puro) ---
# Esta regra pode ser redundante se o R2 for puramente um cliente e não estiver atuando como servidor na porta 1194.
# Se você pretende que o R2 também tenha um servidor OVPN na porta 1194, descomente e use-a.
# /ip firewall filter add chain=input dst-port=$ovpnPort protocol=tcp action=accept comment="Permitir conexoes OVPN para R2 (se servidor)";

:delay 5s;
:log info "Configuracao do Cliente OVPN no R2 concluida.";
