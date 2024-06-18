---
title: Configuration raid, et installation de Proxmox 8
description: ""
date: 2023-12-15T19:51:20.693Z
preview: ""
draft: true
tags: []
categories: []
---




# Le ZFS
Parlons ZFS !

ZFS est une combinaison de gestionaire de volume logique et de system de fichier tres avancé qui offre une gestion de stockage de pointe pour les systeme informatique moderne, qui offre de multitiude de bien
## Mais dis moi jamy comment est structuré ZFS ?
![](/Articles/2023-08-28-configuration-raid,-et-installation-de-proxmox 8/le-paint.png)
Bon pas les moyen de faire une maquette a la jamy mais j'ai quand meme  sorti le Paint pour un schema de qualité
### zpool
### vdevs
#### vdev de stockage
#### vdev de support
### device

Comme expliqué precedement zfs gerent les

#### 🐮 CoW: Copy-on-write
Pour garantir une intégrité des données, plutôt que de modifier directement les blocks existantes, ZFS crée une copie des blocks d'origine avant de les modifier , Cela signifie que les blocks d'origine restent intactes jusqu'à ce que la copie modifiée soit écrite avec succès, ceci peut aider en cas de crash a ne pas avoir de données corrompu

#### 📸 les Snapshots et rollback
Ces Snapshots permettent de capturer l'état d'un système de fichiers à un moment précis. Vous pouvez également créer des clones de ces Snapshots, ce qui est extrêmement utile pour tester des configurations ou restaurer des données( surtout dans le cas d'un environment de test ). Et si quelque chose tourne mal, il est possible de revenir en arrière (rollback) en toute simplicité.

#### 📦 Compression et Déduplication
ZFS propose des fonctionnalités avancées de compression avec plusieurs algorithms supporté, et de déduplication des données. La compression permet d'économiser de l'espace de stockage, tandis que la déduplication élimine les doublons, Cela peut entraîner pas mal de gains d'espace, en particulier dans les environnements de stockage de gros volume.

#### ↗️ Scalabilité des pools
ZFS est hautement évolutif, ce qui signifie que vous augmenter votre zpool avec des vdev de stockage ou de support, et ceux meme a chaud ( si votre materiel le permet ) sans interuption des services.
donc on peut augmenter le stockage sans avoir a migrer aucune données

#### ⚡Performance
le coeur des performance reside en sa repartition des données et l'utilisation de cache( arc,l2arc,slog...) pour y stocker les données frequement utilisée, reduisant ainsi les temps d'acces et en ammeliorant les i/o





Maintenan que que la partie hardware est fini on passe au software pour installer Proxmox et Docker
## La to-do list
- Configuration des controleur de stockage
- Installation de proxmox 8.1
- Configuration et optimisation de proxmox
- Creation d'une machine virtuel pour host Docker et Portainer

## configuration des controleur de stockage

pour fair un topo sur la partie stockage
- 2 x SSD SATA 240go sur le controleur SATA de la carte mere que on va configurer en RAIDZ1 pour l'Os 
- 3 x HDD SAS 1To sur le controleur PCI Smart HBA H240 que on va configurer en RAIDZ5
- 1 x SSD NVMe 256go


on comence par passer les deux controleur en mode HBA
o
pour le controleur integré 
F9 > System Configuration > BIOS/Plateform configuration RBSU > system option > SATA COntroler Options > Embedd ed SATA Configuration
et on choisi Enable SATA AHCI Support

pour le controleur SAS Smart HBA H240 faut passer par l'outils HPE Smart Storage Administrator pour le configurer
F9 > Embedded Applicaitons > intelligent Provisioning > HPE Smart Storage Administrator 

![](/Articles/2023-08-28-configuration-raid,-et-installation-de-proxmox 8/HPLOCONS_spzuXMF5d4.png)
et on clique sur configure puis cancel RAID mode
![](/Articles/2023-08-28-configuration-raid,-et-installation-de-proxmox 8/HPLOCONS_K0esG1dDwI.png)S