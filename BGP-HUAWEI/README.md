# Configuração BGP - Huawei

A configuração abaixo descreve, em um único fluxo, a criação de Loopback para Router-ID, habilitação do BGP, criação de rotas blackhole, anúncios de prefixos, filtros com ip-prefix e route-policy, configuração de peers BGP e comandos de verificação e troubleshooting em roteadores Huawei.

Primeiramente, cria-se uma interface Loopback para garantir estabilidade do Router-ID, já que ela não depende de links físicos:
```bash
system-view
interface LoopBack0
 ip address 5.0.0.1 255.255.255.255
quit

Em seguida, habilita-se o processo BGP definindo o ASN e o router-id, normalmente utilizando o IP da Loopback:

bgp 65000
 router-id 5.0.0.1

Para divulgação de prefixos null, geralmente utilizados em cenários de mitigação de DDoS, configura-se uma rota blackhole:

ip route-static 181.200.1.1 255.255.252.0 null 0

Como exemplo, abaixo um bloco /22 quebrado em dois /23 e quatro /24, todos apontando para NULL0:

ip route-static 200.200.0.0 255.255.252.0 NULL0
ip route-static 200.200.0.0 255.255.254.0 NULL0
ip route-static 200.200.2.0 255.255.254.0 NULL0
ip route-static 200.200.0.0 255.255.255.0 NULL0
ip route-static 200.200.1.0 255.255.255.0 NULL0
ip route-static 200.200.2.0 255.255.255.0 NULL0
ip route-static 200.200.3.0 255.255.255.0 NULL0

Somente redes presentes na RIB (tabela de roteamento) podem ser anunciadas via BGP. O anúncio pode ser feito de forma agregada ou desagregada:

bgp 65000
 network 200.200.0.0 255.255.252.0
 network 200.200.0.0 255.255.254.0
 network 200.200.2.0 255.255.254.0
 network 200.200.0.0 255.255.255.0
 network 200.200.1.0 255.255.255.0
 network 200.200.2.0 255.255.255.0
 network 200.200.3.0 255.255.255.0

Para controlar quais prefixos serão anunciados, cria-se um ip-prefix permitindo anúncios do /22 ao /24:

ip ip-prefix MEUS-BLOCOS index 100 permit 200.200.0.0 22 greater-equal 22 less-equal 24

Esse ip-prefix é aplicado em uma route-policy de saída:

route-policy AS-OUT permit node 10
 if-match ip-prefix MEUS-BLOCOS

Caso seja necessário anunciar full route ou apenas default route, cria-se um ip-prefix específico e aplica-se em outra route-policy:

ip ip-prefix FULL-ROUTE index 10 permit 0.0.0.0 0 greater-equal 8 less-equal 24
ip ip-prefix FULL-ROUTE index 20 permit 0.0.0.0 0
route-policy AS-OUT permit node 10
 if-match ip-prefix FULL-ROUTE

Para importação de rotas, pode-se filtrar prefixos específicos recebidos do peer criando um ip-prefix de entrada:

ip ip-prefix AS-IN index 100 permit 181.41.200.0 22 greater-equal 22 less-equal 24
route-policy AS-IN permit node 10
 if-match ip-prefix AS-IN

Da mesma forma, para importar full route ou default route:

ip ip-prefix FULL-ROUTE index 10 permit 0.0.0.0 0 greater-equal 8 less-equal 24
route-policy AS-IN permit node 10
 if-match ip-prefix FULL-ROUTE

Após a criação de todas as route-policies, configura-se o vizinho BGP (peer), ajustando IP e ASN conforme a topologia:

bgp 65000
 peer 1.1.1.1 as-number 6001
 peer 1.1.1.1 description PEER-UPSTREAM
 peer 1.1.1.1 route-policy AS-IN import
 peer 1.1.1.1 route-policy AS-OUT export

A sessão BGP pode ser verificada com:

display bgp peer

Para validação dos anúncios e recebimentos, utilizam-se os seguintes comandos:

display bgp routing-table peer 200.200.0.1 advertised-routes
display bgp routing-table peer 200.200.0.1 received-routes
display bgp routing-table 0.0.0.0 0

Como exemplo adicional de configuração de peer em outro ASN:

bgp 6002
 peer 200.200.0.1 as-number 6001
 peer 200.200.0.1 description PEER-UPSTREAM

Para remover uma route-policy existente:

undo route-policy NOME_DA_LISTA

Por fim, alguns comandos úteis de troubleshooting BGP:

display bgp summary
display bgp peer
display bgp routing-table | include 181.200