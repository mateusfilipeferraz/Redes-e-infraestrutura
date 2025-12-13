````markdown
# IPv6 Huawei

## Configuração Básica IPv6

```text
system-view
ipv6

interface GE1/0/0
 ipv6 address auto link-local
 ipv6 enable
 ipv6 address 2001:db8:1::1/64
 quit
````

---

## BGP IPv6

### Criar rota blackhole

```text
ipv6 route-static 2001:db8:2:: 48 NULL 0
```

---

### Criar prefix-lists IPv6

```text
ip ipv6-prefix FULLROUTE index 100 permit 2000:: 32 greater-equal 32 less-equal 48
ip ipv6-prefix MEUS-BLOCO-IPV6 index 100 permit 2001:db8:2:: 48
```

---

### Criar route-policy

```text
route-policy AS-6500-IPV6-IN permit node 100
 if-match ipv6 address prefix-list FULLROUTE

route-policy ASN-65000-IPV6-OUT permit node 100
 if-match ipv6 address prefix-list MEUS-BLOCO-IPV6
```

---

### Configuração do BGP IPv6

```text
<R1> system-view
Enter system view, return user view with Ctrl+Z.

[R1] bgp 17821
[R1-bgp] undo default ipv4-unicast

[R1-bgp] group IPv6-eBGP-AS45192 external
[R1-bgp] peer 2001:df2:ee01:10::1 as-number 45192

[R1-bgp] ipv6-family unicast
[R1-bgp-af-ipv6] peer IPv6-eBGP-AS45192 enable
[R1-bgp-af-ipv6] peer 2001:df2:ee01:10::1 group IPv6-eBGP-AS45192
[R1-bgp-af-ipv6] quit
[R1-bgp] quit
```

---

### Aplicar route-policy de saída

```text
[R1] bgp 17821
[R1-bgp] ipv6-family unicast
[R1-bgp-af-ipv6] peer IPv6-eBGP-AS45192 route-policy AS45192_IPV6_OUT export
[R1-bgp-af-ipv6] quit
[R1-bgp] quit
[R1] quit
```

```
```
