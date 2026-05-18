# 02 — Segmentation Réseau par VLAN (Cisco)

## 🎯 Objectif
Mettre en place une segmentation réseau par VLAN afin d'isoler les différents flux (administration, utilisateurs, serveurs, DMZ) et améliorer la sécurité globale du réseau.

---

## 🏗️ Architecture

```
                        [Routeur Principal]
                               |
                    [Switch Core - Trunk]
                    /          |          \
             VLAN 10        VLAN 20      VLAN 30
           [Direction]    [Employés]   [Serveurs]
          192.168.10.0   192.168.20.0  192.168.30.0
```

---

## 📋 Plan d'adressage

| VLAN | Nom | Réseau | Passerelle |
|---|---|---|---|
| 10 | Direction | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Employes | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Serveurs | 192.168.30.0/24 | 192.168.30.1 |
| 99 | Management | 192.168.99.0/24 | 192.168.99.1 |

---

## ⚙️ Configuration Cisco IOS

### Création des VLANs
```
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name Direction
Switch(config)# vlan 20
Switch(config-vlan)# name Employes
Switch(config)# vlan 30
Switch(config-vlan)# name Serveurs
Switch(config)# vlan 99
Switch(config-vlan)# name Management
```

### Ports Access (postes clients)
```
Switch(config)# interface range fa0/1-5
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 20
Switch(config-if-range)# spanning-tree portfast

Switch(config)# interface range fa0/6-10
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
```

### Ports Trunk (entre switches / routeur)
```
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30,99
Switch(config-if)# switchport trunk native vlan 99
```

### Inter-VLAN Routing (Router-on-a-stick)
```
Router(config)# interface gigabitEthernet 0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0

Router(config)# interface gigabitEthernet 0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0

Router(config)# interface gigabitEthernet 0/0.30
Router(config-subif)# encapsulation dot1Q 30
Router(config-subif)# ip address 192.168.30.1 255.255.255.0
```

### Sécurisation des ports inutilisés
```
Switch(config)# interface range fa0/20-24
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 999
Switch(config-if-range)# shutdown
```

---

## ✅ Vérification

```
Switch# show vlan brief
Switch# show interfaces trunk
Switch# show spanning-tree vlan 10
Router# show ip route
```

---

## 🎓 Compétences acquises

- Création et gestion de VLANs sur switch Cisco
- Configuration de ports Access et Trunk
- Routage inter-VLAN (Router-on-a-stick)
- Sécurisation des ports inutilisés
- Isolation des flux réseau par segment
