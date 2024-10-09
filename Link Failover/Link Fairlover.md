
![Minha imagem]Redes-e-infraestrutura/Link Failover/Link Failover.png


Link download do laboratório => https://drive.google.com/file/d/1gt_O3p0Ky5laeuP_ONNJjR8vlHHHDxKR/view?usp=drive_link

# Configuração de Link Failover no MikroTik

O **Failover de link** é uma funcionalidade essencial em redes para garantir que, em caso de falha do link principal, o tráfego seja redirecionado automaticamente para um link de backup. No MikroTik, o Failover pode ser implementado de forma simples ou avançada, dependendo das necessidades da rede.

## Configuração Básica de Failover no MikroTik

A forma mais comum de implementar Failover no MikroTik é por meio de rotas estáticas com diferentes valores de **distance** (distância administrativa). A rota com a menor distância será usada como a principal, e a rota com a distância maior será usada como backup.

### Passos para Configuração

1. **Adicionar Rotas para os Links WAN**:
   Adicione duas rotas estáticas, uma para o link principal e outra para o link de backup.

   ```bash
   /ip route
   add dst-address=0.0.0.0/0 gateway=<gateway_do_link_primário> distance=1
   add dst-address=0.0.0.0/0 gateway=<gateway_do_link_secundário> distance=2

    Habilitar o Monitoramento do Gateway: Para garantir que o Failover funcione corretamente, habilite o monitoramento do gateway. Isso fará com que o MikroTik pingue o gateway do link primário para verificar sua conectividade.

    bash

    /ip route
    set [find gateway=<gateway_do_link_primário>] check-gateway=ping

    Teste o Failover: Desconecte o link principal fisicamente ou desligue o modem do ISP para verificar se o roteador alterna para o link secundário.

Configuração de Failover Avançada (Script de Monitoramento)

Para uma solução mais robusta, é possível utilizar scripts para verificar a conectividade externa, por exemplo, pingando o DNS do Google (8.8.8.8). Isso assegura que o Failover ocorra apenas quando a conectividade à internet realmente estiver interrompida.
Exemplo de Script de Failover

    Criar Script de Monitoramento:

    bash

/system script
add name=check-internet source={
    :if ([/ping 8.8.8.8 count=3] = 0) do={
        /ip route set [find gateway=<gateway_do_link_secundário>] distance=1;
        /ip route set [find gateway=<gateway_do_link_primário>] distance=2;
    } else={
        /ip route set [find gateway=<gateway_do_link_primário>] distance=1;
        /ip route set [find gateway=<gateway_do_link_secundário>] distance=2;
    }
}

Agendar o Script para Rodar Periodicamente:

bash

    /system scheduler
    add name=failover interval=1m on-event=check-internet

    Este script será executado a cada minuto para verificar a conectividade e fazer o Failover automaticamente, se necessário.

Configuração de Failover com Roteamento Dinâmico (OSPF/BGP)

Se a rede é mais complexa, utilizando múltiplos sites ou múltiplos provedores, você pode configurar o Failover utilizando protocolos de roteamento dinâmico, como OSPF ou BGP.
OSPF (Open Shortest Path First)

O OSPF é um protocolo de roteamento dinâmico que recalcula as rotas automaticamente em caso de falhas, garantindo Failover entre roteadores e links.

    Configure interfaces OSPF e adicione as redes participantes.
    O OSPF irá recalcular as rotas automaticamente ao detectar uma falha de link.

BGP (Border Gateway Protocol)

O BGP é utilizado em cenários de redes multi-homed (com múltiplos ISPs) para garantir Failover entre diferentes provedores de serviços de internet. Ele ajusta as rotas com base nas mudanças na topologia da rede.
Benefícios do Failover no MikroTik

    Alta disponibilidade: Garante que a rede continue operando mesmo em caso de falha do link principal.
    Eficiência: A configuração com rotas estáticas é simples de implementar e atende à maioria das pequenas e médias redes.
    Flexibilidade: Suporte a scripts e protocolos de roteamento dinâmico permite soluções personalizadas para redes mais complexas.

Considerações Finais

O Failover no MikroTik é uma solução poderosa para garantir a continuidade dos serviços de rede, seja em cenários simples com rotas estáticas ou em redes mais complexas utilizando protocolos dinâmicos como OSPF e BGP. A flexibilidade e as opções de customização fazem do MikroTik uma escolha excelente para a implementação de alta disponibilidade em redes.
