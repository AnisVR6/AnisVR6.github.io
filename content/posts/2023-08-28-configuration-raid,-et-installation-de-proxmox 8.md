---
title: Configuration raid, et installation de Proxmox 8
description: ""
date: 2023-08-27T22:29:41.487Z
preview: ""
draft: true
tags: []
categories: []
---


Maintenant que tout est monté, passons aux reglage
#### Creation d'un RAID 5 avec HPE Smart Storage Administrator

avec les 3 disque je vais créér une  RAID 5 le compromis idéal pour ne pas perdre trop d'espace et garantie une securité des données correcte, je peux perdre 1 disque sans perdre mes données,
Pour lancer l'assistant HPE Smart Storage Administrator
F9 > System Utilities > embededed Aplications > Intelligent Provisioning > HPE Smart Storage Administrator

dans la listes des dispositifs accessibles on peut voir que les deux controleur raid la B140i et la HBA H240 celle que je vais utiliser et qui support les disque SAS contrairment a l'autre
et tout en bas on peut voir le SSD NVMe qui est detecté aussi
on passe a la configuration

on clique sur Create Array

on selection les 3 disques et on passe a l'ecran suivant
Le CPU est détecté sans soucis, nous faisons un petit memtest86.
