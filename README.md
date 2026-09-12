
##  Configuration du Lab de Simulation

### Préréquis & Environnement
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
1.installer virtualbox () et telecharger une ISO à (https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/)  
2. Création de la VM principale `PC1` sous Debian 12 (Netinst)  
3. Allocation des ressources : 1 vCPU, 1024 Mo de RAM, 10 Go de disque virtuel dynamiquement alloué  
4. Clonage de la VM pour générer rapidement les nœuds secondaires (`PC2`), en régénérant les adresses MAC pour éviter tout conflit de niveau 2  
