<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/HUAWEI/DOCUMENTA%C3%87%C3%83O%20HUAWEI/QINQ.802.1AD/image-20260108-235206.png"  width="600" />
````markdown
# Topologia QinQ – Mikrotik (CEO / SWPE / CE)

---

## CEO1

```routeros
/interface ethernet
set [ find default-name=ether1 ] disable-running-check=no name=ether1-OPERADORA

/interface vlan
add interface=ether1-OPERADORA name=vlan10 vlan-id=10
add interface=ether1-OPERADORA name=vlan20 vlan-id=20
add interface=ether1-OPERADORA name=vlan30 vlan-id=30

/ip address
add address=192.168.10.1/24 interface=vlan10 network=192.168.10.0
add address=192.168.20.1/24 interface=vlan20 network=192.168.20.0
add address=192.168.30.1/24 interface=vlan30 network=192.168.30.0
````

---

## SWPE01

```routeros
/interface bridge
add name=bridge1 vlan-filtering=yes

/interface ethernet
set [ find default-name=ether1 ] disable-running-check=no name=ether1-CE01
set [ find default-name=ether2 ] disable-running-check=no name=ether2-SWPE02

/interface bridge port
add bridge=bridge1 ingress-filtering=no interface=ether1-CE01 pvid=1900 tag-stacking=yes
add bridge=bridge1 interface=ether2-SWPE02

/interface bridge vlan
add bridge=bridge1 tagged=ether2-SWPE02 untagged=ether1-CE01 vlan-ids=1900
```

---

## SWPE02

```routeros
/interface bridge
add name=bridge1

/interface ethernet
set [ find default-name=ether1 ] disable-running-check=no name=ether1-SWPE01
set [ find default-name=ether2 ] name=ether2-SWPE03

/interface bridge port
add bridge=bridge1 interface=ether1-SWPE01
add bridge=bridge1 interface=ether2-SWPE03

/interface bridge vlan
add bridge=bridge1 tagged=ether1-SWPE01,ether2-SWPE03 vlan-ids=1900
```

---

## SWPE03

```routeros
/interface bridge
add name=bridge1 vlan-filtering=yes

/interface ethernet
set [ find default-name=ether1 ] disable-running-check=no name=ether1-CE02
set [ find default-name=ether2 ] disable-running-check=no name=ether2-SWPE02

/interface bridge port
add bridge=bridge1 ingress-filtering=no interface=ether1-CE02 pvid=1900 tag-stacking=yes
add bridge=bridge1 interface=ether2-SWPE02

/interface bridge vlan
add bridge=bridge1 tagged=ether2-SWPE02 untagged=ether1-CE02 vlan-ids=1900

/ip dhcp-client
add interface=ether1-CE02
```

---

## CE02

```routeros
/interface ethernet
set [ find default-name=ether1 ] disable-running-check=no name=ether1-OPERADORA

/interface vlan
add interface=ether1-OPERADORA name=vlan10 vlan-id=10
add interface=ether1-OPERADORA name=vlan20 vlan-id=20
add interface=ether1-OPERADORA name=vlan30 vlan-id=30

/ip address
add address=192.168.10.2/24 interface=vlan10 network=192.168.10.0
add address=192.168.20.2/24 interface=vlan20 network=192.168.20.0
add address=192.168.30.2/24 interface=vlan30 network=192.168.30.0

/ip dhcp-client
add interface=ether1-OPERADORA
```
