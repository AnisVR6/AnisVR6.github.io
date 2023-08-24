---
title: Mise a jour du firmware d'un HPE ML150 gen9
description: ""
date: 2023-08-22T20:46:27.707Z
preview: /Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/hpe_spp.png
draft: false
tags:
  - hpe
  - serveur
  - homelab
  - tutoriel
categories:
  - Homelab
---
![](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/hpe_spp.png)
## Intro

Il y a quelques jours, j'ai eu la chance de mettre la main sur un lot de deux serveurs d'occase,
![photo des serveurs sur le bullitt](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/srvXbullitt.png)
un HPE ProLiant ML150 Gen9 en dual socket LGA2011-3 mais équipé que d'un seul Xeon E5-2609v4 à 8 cœurs et 16 threads. Je prévois de le remplacer par un Xeon E5-2690v4, qui est en 14 cœurs et 28 threads, et peut-être d'ajouter un second processeur à l'avenir. Il est également équipé de 48 Go de RAM DDR4 ECC.

![HPE ProLiant ML150 Gen9](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/hpe.jpeg)

Ensuite, il y a un Fujitsu Server PRIMERGY TX150 S8, qui est équipé d'un Xeon E5-2403 à 4 cœurs et 4 threads, ainsi que de 8 Go de RAM DDR3 ECC. Cette machine est un peu limitée en termes de performances, je doute que je lui trouverai une utilisation. Malheureusement, ces serveurs étaient vendus en lot, ce qui signifie que je me retrouve avec un "cale-porte" encombrant.

## Le projet

Comme vous pouvez l'imaginer, je vais principalement utiliser le HPE ProLiant ML150 Gen9, qui rejoindra mon HomeLab. Sur ce serveur, je prévois d'installer un hyperviseur Proxmox 8, ainsi que Docker et Portainer pour avoir une interface propre et conviviale pour gérer les conteneurs.

Avant de pouvoir réaliser ce projet, je vais devoir effectuer quelques mises à niveau :
- Remplacer le CPU actuel par le Xeon E5-2690v4.
- Installer un SSD NVMe à l'aide d'un adaptateur M.2 vers PCIe, car il n'y a pas de slot M.2 disponible.
- Imprimer des caddy pour pouvoir installer mes trois disques SAS.

Cependant, avant de procéder à ces étapes, la première étape essentielle est de mettre à jour le firmware du serveur HPE.

## Création d'une clé USB bootable

### Récupération de l'ISO

Tout d'abord, il faut télécharger l'ISO *HPE Service Pack for ProLiant Gen9* correspondant voici la dernier en date :

```
P52574_001_spp-Gen9.1-Gen9SPPGen91.2022_0822.4.iso
```

Le problème c'est que cette version n'est disponible qu'en ayant une souscription au support HP. Donc dans ce cas, il faudra trouver l'iso approprié ailleurs. Vous pouvez utiliser le hachage du fichier pour vous assurer de la correspondance :

```
cbd7a2a1d1aa4bbad95797dd281acf89910f9054d36d78857ee3e8c5fe625790
```

Pour vérifier le hachage du fichier téléchargé, on utilise la commande PowerShell suivante :

```powershell
Get-FileHash .\P52574_001_spp-Gen9.1-Gen9SPPGen91.2022_0822.4.iso -Algorithm SHA256 | Format-List
```

On obtient le résultat du hachage comme suit :

```powershell
Algorithm : SHA256
Hash      : CBD7A2A1D1AA4BBAD95797DD281ACF89910F9054D36D78857EE3E8C5FE625790
Path      : C:\Users\anisd\Desktop\Tixati_portable\downloads\P52574_001_spp-Gen9.1-Gen9SPPGen91.2022_0822.4.iso
```

Ensuite, on peut comparer les deux hachages avec la commande suivante :

```powershell
"hash1" -eq "hash2"
```

![Comparaison des hachages](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822205644.png)

Le fichier n'a pas été altéré, on peut continuer.

### Montage du fichier ISO

On monte le fichier ISO.

![Contenu de l'ISO](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822205835.png)

## Création de la clé USB avec l'USB Key Utility

Ensuite, on va devoir utiliser l'outil USB Key Utility disponible dans le dossier suivant :

```
iso:\usb\usbkey
```

pour créer notre clé USB bootable.

![Utilisation de l'USB Key Utility](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822211511.png)

j'ai choisit la première option, car j'ai a qu'un seul serveur HPE.

![Sélection de l'option de création de clé USB](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822211632.png)

Pour la source, si on a précédemment monté l'ISO, le logiciel devrait la détecter automatiquement ainsi que la destination. Sinon, on peut toujours modifier si ce ne sont pas les bonnes ou même choisir directement l'ISO.

![Configuration de la source et de la destination](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822212223.png)

On attend la copie des fichiers.

![Copie des fichiers en cours](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822212454.png)

Une fois finie, on peut retirer la clé bien sûr en prenant soin de l'éjecter correctement, ensuite reste plus qu'a la brancher sur le serveur.

![Retrait de la clé USB](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822213645.png)

## Démarrage du serveur

On démarre le serveur et on le laisse booter depuis la clé tout seule. Si on utilise le boot menu F11, le serveur démarre en mode legacy ce qui peut causer des problèmes.

![Menu de démarrage depuis la clé USB](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822215323.png)

On arrive sur le menu de sélection pour choisir le mode d'installation. On va continuer en mode interactif pour montrer toutes les options et procédures possibles.

![Sélection du mode d'installation](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822215623.png)

On choisit la langue et on accepte la licence EULA.

![Sélection de la langue et acceptation de l'EULA](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822220013.png)

Ensuite, on continue sur la mise à "Firmware Update".

![Sélection de la mise à jour du firmware](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822220123.png)

L'utilitaire fait l'inventaire des composants à mettre à jour. Une fois fini, on peut passer à la suite.

![Inventaire des composants à mettre à jour](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822220252.png)

On se retrouve sur cet écran où on peut choisir les composants à mettre à jour. J'avais déjà mis à jour la partie ILO et la carte réseau pour éviter les interruptions de la console pendant la mise à jour et pour pouvoir faire les captures d'écran pour cet article. On sélectionne les composants et on clique sur "Deploy".

![Sélection des composants à mettre à jour](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822221406.png)

On attend la fin de la mise à jour.

![Mise à jour en cours](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822221502.png)

Une fois finie, on se retrouve avec cet écran récapitulatif où on peut voir si tout s'est passé correctement et accéder aux journaux pour le débogage si un composant ne s'est pas mis à jour. On clique sur "Reboot" pour finaliser tout ça.

![Écran récapitulatif de la mise à jour](/Articles/2023-08-22-mise-a-jour-du-firmware-d'un-hpe-ml150-gen9/Pasted%20image%2020230822221752.png)



Nous voilà avec un serveur à jour au niveau du firmware et du BIOS. Il ne nous reste plus qu'à effectuer la mise à niveau des composants et à passer à l'installation du système. Je détaillerai tout cela dans d'autres articles.

À bientôt.