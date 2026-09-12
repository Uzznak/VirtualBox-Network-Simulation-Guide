
##  Configuration du Lab de Simulation

### Environnement
* **Hyperviseur :** Oracle VirtualBox 7.x
* **Système d'exploitation hôte :** Ubuntu 24.04 LTS
* **Distribution des nœuds virtuels :** Debian 12 (Bookworm) 64-bit

### Architecture de la Topologie Réseau  
Afin de simuler le domaine de diffusion d'un LAN sans surcharger les ressources système, la topologie est modélisée à l'aide de machines virtuelles interconnectées sur un réseau interne virtuel  configuré en mode promiscuité.

| Nœud | RAM | CPU | Disque | Rôle / Description |
| :--- | :--- | :--- | :--- | :--- |
| **PC1** | 1024 Mo | 1 vCPU | 10 Go | Station cliente 1 (Émetteur / Analyseur `tcpdump`) | 
| **PC2** | 1024 Mo | 1 vCPU | 10 Go | Station cliente 2 (Cible / Générateur de trafic) |  

  
### Étapes d'installation des VM
### Créer la première machine virtuelle

1.installer virtualbox () et telecharger une ISO à (https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/)  
2. Création de la VM principale `PC1` sous Debian 12 (Netinst)  
3. Allocation des ressources : 1 vCPU, 1024 Mo de RAM, 10 Go de disque virtuel dynamiquement alloué  

## Configuration de l'Interconnexion Réseau (VirtualBox)
### configurer le réseau
Le but est de simuler le comportement d'un **Hub Ethernet (Layer 1)**.
Le commutateur virtuel de VirtualBox est configuré pour transmettre la totalité du trafic unicast et broadcast à l'ensemble des nœuds raccordés.

#### Etapes 
1. **Réseau Interne Partagé :**  
(ne pas démarrer PC1) :Faire un clic droit sur PC1 > Configuration  
Va dans l'onglet Réseau (Network) :  
   * **Mode d'accès reseau :** sélectionner Réseau interne (Internal Network, créer LAN_HUB (nom du réseau virtuel partagé)  
   * **Mode promiscuité :** `Déroule la section Avancé :M Choisis Autoriser tout (Allow All) (Permet la capture de toutes les trames circulantes via `tcpdump` / Wireshark)  
valider et fermer


4. Clonage de la VM pour générer rapidement les nœuds secondaires (`PC2`), en régénérant les adresses MAC pour éviter tout conflit de niveau 2  

3. **Dédoublement des nœuds :**
   * Génération de `PC2` par clonage de la machine matrice `PC1`.
   * Réinitialisation des adresses MAC physiques virtuellement attribuées aux interfaces réseau afin de garantir l'unicité des tables ARP[cite: 1].
