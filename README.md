# 🛡️ Infrastructure Pare-feu pfSense & Accès Distant OpenVPN

> Réalisation d'un projet d'infrastructure et de cybersécurité visant à déployer et sécuriser l'accès distant d'un collaborateur vers un réseau privé d'entreprise, à travers un pare-feu **pfSense** et un tunnel chiffré **OpenVPN**.

---

## 🎯 Objectifs & Réalisations

* **Virtualisation & Architecture Réseau :** Instanciation du pare-feu **pfSense 2.7.2** sur **VMware Workstation** avec segmentation stricte entre le réseau externe (`WAN` en `192.168.23.142/24`) et le réseau privé (`LAN` en `192.168.1.1/24`).
* **Infrastructure à Clés Publiques (PKI) :** Déploiement d'une Autorité de Certification interne (`VPN-CA`) avec chiffrement `RSA 2048 bits` / `SHA-256` pour la signature et la gestion du cycle de vie des certificats.
* **Déploiement VPN Distant :** Configuration du serveur **OpenVPN** sur l'interface `WAN` (port `UDP 1194`), définition de la plage virtuelle d'interconnexion (`10.0.8.0/24`), publication des routes `LAN` et génération automatique des règles de filtrage.
* **Gestion des Accès & Profils :** Création de l'utilisateur dédié (`arda-vpn`), association du certificat client et exportation du fichier de configuration unifié `.ovpn` via le package *Client Export*.
* **Recette & Validation :** Importation du profil dans le client **OpenVPN Connect**, établissement du tunnel chiffré et validation du franchissement de pare-feu par tests d'écho ICMP (`ping 192.168.1.1` réussi avec `0%` de perte).

---

## 🛠️ Environnement technique

`pfSense 2.7.2` • `OpenVPN` • `PKI / CA (SSL/TLS)` • `OpenVPN Connect` • `VMware Workstation` • `Routage & Filtrage IP` • `ICMP`

---

📄 **[Consulter le rapport complet du projet (PDF)](./rapport-pfsense-openvpn.pdf)**
