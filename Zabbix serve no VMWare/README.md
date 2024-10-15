Zabbix é uma solução de monitoramento **open-source** e altamente escalável, utilizada para supervisionar redes, servidores, aplicações, serviços em nuvem e dispositivos IoT. Ele oferece monitoramento baseado em agentes ou sem agente, coletando métricas como uso de CPU, memória, tráfego de rede, entre outros. O Zabbix pode emitir alertas via e-mail, SMS, e outros canais quando determinadas condições são atingidas, além de fornecer gráficos e dashboards customizáveis para visualização de dados históricos e em tempo real.

### Funcionalidades principais:

- **Descoberta automática de dispositivos**
- **Verificação de disponibilidade** (ping, SNMP, HTTP)
- **Monitoramento de logs**
- **Automação de ações** (como reiniciar serviços)
- **Suporte a templates** para facilitar a configuração

Por ser altamente configurável e extensível, o Zabbix é amplamente utilizado em ambientes corporativos de pequeno a grande porte.

Eu disponibilizei uma imagem do zabbix 7.0 instalada sobre o Ubuntu 22.4 totalmente funcional bastando baixar e abrir ela no 
seu VMWare.

### Link de download da imagem Zabbix:

[Baixar imagem Zabbix](https://drive.google.com/file/d/1s4CC6HW0vegTuLko47coUBWyYmTUPvVj/view?usp=sharing)

### Dados de acesso:

- **Senha do usuário (Ubuntu)**: `ubuntu`
- **Senha**: `123456`
- **Senha do root**: `123456`
- **Senha do banco de dados MariaDB**: `zabbixx`

### Acesso à interface web do Zabbix:

- **Usuário**: `Admin`
- **Senha**: `zabbix`

### Mudanças na interface de rede para permitir o acesso à internet:

Para configurar a interface de rede e garantir acesso à internet, edite o arquivo de rede com o comando:

```bash
nano /etc/netplan/00-installer-config.yaml

# This is the network config written by 'subiquity'
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.18.128/24
      routes:
        - to: default
          via: 192.168.18.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1

Após realizar as alterações, aplique as mudanças com o comando:

netplan apply

Configuração Principal do Zabbix Server:

O arquivo de configuração principal do Zabbix Server está localizado em:

/etc/zabbix/zabbix_server.conf

Nesse arquivo, você pode ajustar várias opções, como:

    DBHost: Configuração do banco de dados.
    DBName: Nome do banco de dados do Zabbix.
    DBUser: Usuário do banco de dados.
    DBPassword: Senha do banco de dados.
    ListenPort: Porta de escuta do servidor Zabbix.

Após editar o arquivo, reinicie o serviço do Zabbix para aplicar as mudanças:

    sudo systemctl restart zabbix-server
