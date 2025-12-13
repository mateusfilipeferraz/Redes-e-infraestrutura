````markdown
# Configuração de VLAN e Vlanif (Huawei)

## Criação das VLANs

```text
system-view
vlan 10
vlan 20
````

---

## Configuração das Interfaces Vlanif (Gateway das VLANs)

```text
interface Vlanif 10
 ip address 192.168.3.1 255.255.255.0

interface Vlanif 20
 ip address 192.168.4.1 255.255.255.0
```

---

## Configuração das Portas de Acesso

### Portas na VLAN 10

```text
interface GigabitEthernet1/0/3
 port link-type access
 port default vlan 10

interface GigabitEthernet1/0/4
 port link-type access
 port default vlan 10
```

### Portas na VLAN 20

```text
interface GigabitEthernet1/0/5
 port link-type access
 port default vlan 20

interface GigabitEthernet1/0/6
 port link-type access
 port default vlan 20
```

```
```
