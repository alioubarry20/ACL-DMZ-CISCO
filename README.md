# 🔐 ACL-DMZ-CISCO

> TP de sécurité réseau — Listes de Contrôle d'Accès (ACL) et Zone Démilitarisée (DMZ)  
> Simulé sous **Cisco Packet Tracer**

---

## 📋 Description

Ce projet illustre la mise en place d'une architecture réseau sécurisée pour une entreprise hébergeant son propre site web. L'objectif est de protéger le réseau interne tout en rendant le serveur web accessible depuis internet, grâce aux ACL étendues et à une DMZ.

---

## 🗺️ Topologie

![Architecture réseau](screenshots/architecture.png)

### Les 3 zones

| Zone | Réseau | Rôle |
|---|---|---|
| 🌐 Internet | 216.90.80.0/24 — 216.90.90.0/24 — 216.90.100.0/24 | Réseau public |
| 🟠 DMZ | 10.0.0.0/29 | Héberge le serveur web public |
| 🟢 Entreprise | 172.16.0.0/16 | Réseau privé protégé |

---

## ⚙️ Configurations

Les fichiers de configuration des routeurs sont disponibles dans le dossier [`configs/`](configs/) :

| Fichier | Routeur | Rôle |
|---|---|---|
| `config_router_internet.txt` | Router-PT | Simule internet |
| `config_router2_public.txt` | Router2 (public) | NAT Externe + ACL DMZ |
| `config_router1_private.txt` | Router1 (private) | NAT Interne + ACL entreprise |
| `notes_dns_dhcp.txt` | Server1 | DNS + DHCP interne |

---

## 🔒 Sécurité mise en place

### NAT
- **NAT Statique** : expose le serveur web `10.0.0.3` via l'IP publique `216.90.80.253`
- **NAT Dynamique PAT** : masque le réseau `172.16.0.0/16` derrière l'IP publique `216.90.80.254`

### ACL réseau entreprise (Router1 private — Fa0/0)
```
! En entrée — ACL 101
access-list 101 permit ip 172.16.0.0 0.0.255.255 any

! En sortie — ACL 100
access-list 100 permit tcp any 172.16.0.0 0.0.255.255 established
access-list 100 permit icmp 10.0.0.0 0.0.0.7 172.16.0.0 0.0.255.255
access-list 100 permit icmp any 172.16.0.0 0.0.255.255 echo-reply
```

### ACL DMZ (Router2 public — Fa1/0)
```
! En entrée — ACL 111
access-list 111 permit tcp any host 216.90.80.253 eq 80
access-list 111 permit tcp any 10.0.0.0 0.0.0.7 established
access-list 111 permit icmp any 10.0.0.0 0.0.0.7 echo-reply
access-list 111 permit icmp 172.16.0.0 0.0.255.255 10.0.0.0 0.0.0.7
```

---

## ✅ Tests de validation

| Test | Source | Destination | Résultat |
|---|---|---|---|
| PC0 → internet | 172.16.0.x | 216.90.90.1 | ✅ Autorisé |
| PC2 → réseau entreprise | 216.90.90.1 | 172.16.0.x | ❌ Bloqué |
| PC2 → HTTP serveur web | 216.90.90.1 | http://216.90.80.253 | ✅ Autorisé |
| PC2 → ping serveur web | 216.90.90.1 | 216.90.80.253 | ❌ Bloqué |
| PC0 → ping DMZ | 172.16.0.x | 10.0.0.3 | ✅ Autorisé |

---

## 📁 Structure du projet

```
ACL-DMZ-CISCO/
│
├── README.md
├── configs/
│   ├── config_router_internet.txt
│   ├── config_router2_public.txt
│   ├── config_router1_private.txt
│   └── notes_dns_dhcp.txt
│
├── rapport/
│   └── rapport.md
│
└── screenshots/
    ├── architecture.png
    └── screenshotsDesConfigurations/
        ├── 01_router_internet_interfaces.png
        ├── 02_router_public_interfaces.png
        ├── 03_dhcp_pc1.png
        ├── 04_dhcp_pc2.png
        ├── 05_ping_tests_depuis_pc2.png
        ├── 06_nat_configuration.png
        ├── 07_nat_statique_test.png
        ├── 08_nat_dynamique_pat_test.png
        ├── 09_show_ip_nat_translations.png
        ├── 10_acl_router_private.png
        ├── 11_test_pc0_vers_internet.png
        ├── 12_test_pc2_vers_entreprise_bloque.png
        ├── 13_acl_111_dmz_configuration.png
        ├── 14_debug_acl_dmz.png
        ├── 15_test_pc2_ping_server2_bloque.png
        ├── 16_test_pc2_http_server2.png
        └── 17_test_pc0_ping_server2.png
```

---

## 🛠️ Outils utilisés

![Cisco Packet Tracer](https://img.shields.io/badge/Cisco_Packet_Tracer-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![Cisco IOS](https://img.shields.io/badge/Cisco_IOS-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)

---

## 👤 Auteur

**Aliou Barry**  
Étudiant en L2 Passionée Par les Réseaux et la Cybersecurité  
GitHub : [@alioubarry20](https://github.com/alioubarry20)
