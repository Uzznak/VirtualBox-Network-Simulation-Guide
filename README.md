
##  Configuration du Lab de Simulation

### Environnement
* Hyperviseur : Oracle VirtualBox 7.x
* Système d'exploitation hôte : Ubuntu 24.04 LTS
* Distribution des nœuds virtuels : Debian 12 (Bookworm) 64-bit  

### Architecture de la Topologie Réseau  
Afin de simuler le domaine de diffusion d'un LAN sans surcharger les ressources système, la topologie est modélisée à l'aide de machines virtuelles interconnectées sur un réseau interne virtuel  configuré en mode promiscuité.

| Nœud | RAM | CPU | Disque | Rôle / Description |
| :--- | :--- | :--- | :--- | :--- |
| **PC1** | 1024 Mo | 1 vCPU | 10 Go | Station cliente 1 (Émetteur / Analyseur `tcpdump`) | 
| **PC2** | 1024 Mo | 1 vCPU | 10 Go | Station cliente 2 (Cible / Générateur de trafic) |  

  
### Installation des VM
#### Créer la première machine virtuelle

1.installer virtualbox () et telecharger une ISO à (https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/)  
2. Création de la VM principale `PC1` sous Debian 12 (Netinst)  
3. Allocation des ressources : 1 vCPU, 1024 Mo de RAM, 10 Go de disque virtuel dynamiquement alloué  

### Configuration de l'Interconnexion Réseau
#### configurer le réseau
Le but est de simuler le comportement d'un **Hub Ethernet (Layer 1)** sur VirtualBox
Le commutateur virtuel de VirtualBox est configuré pour transmettre la totalité du trafic unicast et broadcast à l'ensemble des nœuds raccordés.

#### Etapes 

**Réseau Interne Partagé :**

PC 1 clic droit > Settings > > Network > > >  **Mode d'accès reseau :** sélectionner Réseau interne (Internal Network)  
> > > nommer LAN_HUB (nom du réseau virtuel partagé)  
> > >  **Mode promiscuité :** `Déroule la section Avancé :M Choisis Autoriser tout (Allow All) (Permet la capture de toutes les trames circulantes via `tcpdump` / Wireshark)  
valider et fermer



### Clonage de la VM 
   * Génération de `PC2` par clonage de la machine matrice `PC1`.
   * Clonage de la VM pour générer rapidement les nœuds secondaires (`PC2`), en régénérant les adresses MAC pour éviter tout conflit de niveau 2  
   * Réinitialisation des adresses MAC physiques virtuellement attribuées aux interfaces réseau afin de garantir l'unicité des tables ARP[cite: 1].

Étape 2 : Cloner PC1 pour créer PC2

Pour avoir un deuxième PC sur le réseau sans refaire l'installation :

    Fais un clic droit sur PC1 > Cloner... (Clone).

    Nom : Nomme la nouvelle VM PC2.

    Politique d'adresse MAC : Sélectionne Générer de nouvelles adresses MAC pour toutes les cartes réseau (impératif pour éviter les conflits d'IP/MAC).

    Type de clone : Choisis Clone lié (Linked clone, très rapide) ou Clone complet.

    Clique sur Terminer.


### Récupération de Machine Virtuelle via Disque VDI / Descripteur VBOX

En cas de perte d'enregistrement de l'instance dans VirtualBox, deux méthodes de restauration sont applicables sans perte de données :

1. **Restauration native par fichier de définition (.vbox) :**
   * Action : `Machine` $\rightarrow$ `Ajouter...` $\rightarrow$ Sélection de `PC1.vbox`.
2. **Restauration par réassociation du volume disque (.vdi) :**
   * En cas d'altération du fichier XML de définition, création d'une nouvelle VM en raccordant le volume virtuel existant (`PC1-TD1.vdi`) comme disque système principal.
  


 #### Dépannage : Conflit d'UUID (`NS_ERROR_FAILURE 0x80004005`)

Lorsqu'une machine virtuelle est déjà enregistrée dans la base du démon VirtualBox mais absente du panneau d'affichage GUI, la tentative d'ajout du fichier `.vbox` génère un conflit d'identifiant unique (UUID).

**Résolution via la CLI VirtualBox :**
```bash
# Libération de l'identifiant UUID en cache
VBoxManage unregistervm PC1

# Réintégration propre de la VM dans le registre
VBoxManage registervm "$HOME/VirtualBox VMs/PC1/PC1.vbox"
