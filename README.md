# 🛡️ Déploiement et Sécurisation d'une Infrastructure Pare-feu pfSense avec Accès Distant OpenVPN

![pfSense](https://img.shields.io/badge/pfSense-2.7.2--RELEASE-212121?style=for-the-badge&logo=pfsense&logoColor=white)
![OpenVPN](https://img.shields.io/badge/OpenVPN-SSL%2FTLS-EA7E20?style=for-the-badge&logo=openvpn&logoColor=white)
![VMware](https://img.shields.io/badge/VMware-Workstation-607078?style=for-the-badge&logo=vmware&logoColor=white)
![Security](https://img.shields.io/badge/Security-PKI%20%2F%20Firewall-success?style=for-the-badge)

## 📋 Présentation du projet

Ce projet d'infrastructure et de cybersécurité consiste à déployer et sécuriser l'accès distant d'un collaborateur vers un réseau privé d'entreprise à travers un pare-feu **pfSense 2.7.2** virtualisé sur **VMware Workstation**, en s'appuyant sur une **Infrastructure à Clés Publiques (PKI)** interne et un tunnel chiffré **OpenVPN**[cite: 1, 2, 17, 20].

📄 **[Consulter le rapport technique complet (PDF)](docs/Rapport_pfSense_OpenVPN_Arda_YASIN.pdf)**

---

## 🏗️ Architecture Réseau

![Schéma d'architecture réseau](images/architecture-reseau.png)

### Plan d'adressage IP

| Zone / Composant | Interface pfSense | Carte VMware | Adresse IP / Sous-réseau | Rôle |
| :--- | :--- | :--- | :--- | :--- |
| **Poste Client (PC Hôte)** | — | Carte Hôte | `192.168.23.x` | Machine distante initiant la connexion VPN[cite: 1] |
| **pfSense — Côté WAN** | `em0`[cite: 1, 2] | NAT[cite: 5] | `192.168.23.142/24`[cite: 2] | Interface externe exposant le service OpenVPN (UDP 1194)[cite: 1, 17] |
| **Tunnel OpenVPN** | `tun0` (Virtuelle) | — | `10.0.8.0/24`[cite: 1, 17, 19] | Réseau virtuel chiffré attribué aux clients connectés[cite: 1, 19] |
| **pfSense — Côté LAN** | `em1`[cite: 1, 2] | Host-Only[cite: 5] | `192.168.1.1/24`[cite: 2] | Passerelle par défaut du réseau privé interne[cite: 1] |
| **Réseau Privé Entreprise** | — | Host-Only[cite: 5] | `192.168.1.0/24`[cite: 19] | Sous-réseau cible protégé derrière le pare-feu[cite: 1, 19] |

---

## ⚙️ Étapes de Réalisation

### 1. Segmentation & Configuration Réseau
* Déploiement de **pfSense 2.7.2-RELEASE (amd64)** avec deux cartes réseau virtuelles distinctes[cite: 2, 5].
* Attribution de l'interface **WAN (`em0`)** sur le réseau NAT (`192.168.23.142/24`) et de l'interface **LAN (`em1`)** sur le réseau isolé Host-Only (`192.168.1.1/24`)[cite: 1, 2, 5].

### 2. Déploiement de la PKI (Autorité de Certification)
* Création d'une Autorité de Certification interne auto-signée nommée **`VPN-CA`** (`O=Lab-arda, CN=internal-ca, C=FR`) d'une validité de 10 ans[cite: 20].
* Génération du certificat serveur dédié afin d'authentifier le pare-feu auprès des clients distants.

### 3. Configuration du Serveur OpenVPN & Pare-feu
* Mode d'authentification : **`Remote Access (SSL/TLS + User Auth)`**.
* Écoute sur l'interface **WAN** via le protocole **UDP / port `1194`**[cite: 17].
* Paramètres cryptographiques : Chiffrement **AES-256-GCM**, hachage **SHA256** et paramètres Diffie-Hellman **2048 bits**[cite: 17].
* Routage : Définition du tunnel sur **`10.0.8.0/24`** et publication du réseau local **`192.168.1.0/24`**[cite: 19].
* Filtrage : Génération automatique des règles pare-feu autorisant les flux entrants UDP 1194 sur le WAN et le transit dans le tunnel[cite: 18].

![Configuration serveur OpenVPN](images/serveur-openvpn.png)

### 4. Gestion des Utilisateurs & Exportation Client
* Création du compte utilisateur **`arda-vpn`** dans le *User Manager* avec génération de son certificat client personnel[cite: 15, 16].
* Installation du package **`openvpn-client-export`** et exportation du profil unifié **`pfSense-UDP4-1194-arda-vpn-config.ovpn`**[cite: 13, 15].

---

## ✅ Tests & Validation

1. **Établissement du tunnel chiffré :** Importation du profil `.ovpn` dans **OpenVPN Connect**, authentification avec le compte `arda-vpn` et obtention d'une adresse IP virtuelle dans la plage `10.0.8.0/24`[cite: 1, 12, 13].
2. **Validation du routage inter-réseaux (ICMP) :** Exécution d'un `ping 192.168.1.1` depuis la machine hôte (`192.168.23.x`) vers l'interface interne LAN du pare-feu — **4 paquets envoyés, 4 reçus, 0 % de perte**[cite: 1].

![Validation ping ICMP](images/test-ping-icmp.png)

---

## 🔒 Note de Sécurité

Pour des raisons de sécurité, le fichier de configuration client réel (`.ovpn`) ainsi que les clés privées et certificats générés lors de ce laboratoire ne sont pas publiés dans ce dépôt.

---

## 🛠️ Environnement Technique

* **Pare-feu / Routeur :** pfSense 2.7.2-RELEASE[cite: 2]
* **VPN & Cryptographie :** OpenVPN, PKI / CA interne (SSL/TLS, AES-256-GCM, SHA256)[cite: 17, 20]
* **Client VPN :** OpenVPN Connect (Windows)
* **Hyperviseur :** VMware Workstation[cite: 1]
