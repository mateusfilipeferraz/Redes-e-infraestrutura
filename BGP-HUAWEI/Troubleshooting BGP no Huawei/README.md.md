````markdown
## 🧩 Cliente

Para ver o estado geral dos peers BGP configurados no roteador do cliente, utilize:
```bash
display bgp peer
````

Para detalhar informações de um peer específico, incluindo estado da sessão, timers e políticas aplicadas:

```bash
display bgp peer <IP_PEER> verbose
```

Para verificar as rotas BGP recebidas de um peer específico:

```bash
display bgp routing-table receive-peer <IP_PEER>
```

Para verificar as rotas BGP que estão sendo anunciadas para um peer:

```bash
display bgp routing-table advertise-peer <IP_PEER>
```

Para visualizar todas as rotas BGP locais presentes na tabela BGP:

```bash
display bgp routing-table
```

Para conferir as rotas estáticas configuradas no roteador:

```bash
display ip routing-table protocol static
```

Para verificar filtros, route-policies, ip-prefix e demais configurações ativas:

```bash
display current-configuration
```

Para testar conectividade com o peer BGP utilizando um IP de origem específico:

```bash
ping <IP_PEER> -a <IP_LOCAL>
```

---

## 🧩 Operadora

Para verificar o estado dos peers BGP configurados na operadora:

```bash
display bgp peer
```

Para detalhar as informações de um cliente específico:

```bash
display bgp peer <IP_CLIENTE> verbose
```

Para visualizar as rotas recebidas do cliente via BGP:

```bash
display bgp routing-table receive-peer <IP_CLIENTE>
```

Para verificar as rotas anunciadas pela operadora ao cliente:

```bash
display bgp routing-table advertise-peer <IP_CLIENTE>
```

Para conferir os filtros, route-policies e ip-prefix aplicados ao cliente:

```bash
display current-configuration
```

Para visualizar a tabela BGP geral da operadora:

```bash
display bgp routing-table
```

Para testar conectividade com o roteador do cliente usando o IP de origem da operadora:

```bash
ping <IP_CLIENTE> -a <IP_OPERADORA>
```

```
```
