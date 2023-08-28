---
title: Upgrade CPU, instalation des disques SAS et SSD NVMe
description: ""
date: 2023-08-26T17:05:29.560Z
preview: /Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2259.jpg
draft: false
tags:
  - hpe
  - serveur
  - homelab
categories:
  - Homelab
slug: upgrade-cpu-instalation-des-disques-sas-ssd-nvme
---

## La to-do list
![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2259.jpg)
Comme expliqué dans l'article précédent, j'avais quelques upgrade à effectuer sur ce serveur :

- Remplacer le CPU actuel par le Xeon E5-2690v4.
- Installer un SSD NVMe à l'aide d'un adaptateur M.2 vers PCIe, car il n'y a pas de slot M.2 disponible.
- Imprimer des caddy pour pouvoir installer mes trois disques SAS.
### La partie Hardware
#### Le CPU
Pour le CPU, je vais installer un Xeon E5-2690v4 que j'avais récupéré d'un ancien serveur où la carte mère avait rendu l'âme. Ainsi, il trouvera une seconde vie ici.
##### Petit comparatif des specs des deux CPU
|Spécifications du processeur                |Intel® Xeon® Processor E5-2690 v4|Intel® Xeon® Processor E5-2620 v4|
|----------------------|---------------------------------|---------------------------------|
|Nb. de cœurs          |14                                  |8                                |
|Nb. de threads        |28                                  |16                               |
|Fréquence Turbo maxi  |3.50 GHz                            |3.00 GHz                       |
|Fréquence de la technologie Intel® Turbo Boost 2.0|3.50 GHz                         |3.00 GHz                         |
|Fréquence de base     |2.60 GHz                       |2.10 GHz                       |
|Cache                 |35 MB Intel® Smart Cache         |20 MB Intel® Smart Cache         |
|Vitesse du bus        |9.6 GT/s                         |8 GT/s                           |
|Nb. de liens QPI      |2                                |2                                |
|TDP                   |135 W                            |85 W                             |
|[Score single thread](https://www.cpubenchmark.net/compare/2780.2vs2766/%5BDual-CPU%5D-Intel-Xeon-E5-2690-v4-vs-Intel-Xeon-E5-2620-v4)   |2144 *(+30.7%)*                      |1640                           |
|[CPU Mark](https://www.cpubenchmark.net/compare/2780.2vs2766/%5BDual-CPU%5D-Intel-Xeon-E5-2690-v4-vs-Intel-Xeon-E5-2620-v4)     |33021 *(+258.5%)*                           |9210                        |


![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2260.jpg)
En démontant le radiateur, il était évident que la pâte thermique était complètement sèche.
![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2263.jpg)
J'ai un peu galéré à déclipser le CPU de son système de rétention (*en bleu sur la photo*), mais avec un peu de force, j'ai pu le retirer et placer le nouveau CPU dessus.
![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2266.jpg)

On remonte le CPU et on applique une fine couche de pâte thermique en prenant soin de bien nettoyer le radiateur des résidus de l'ancienne pâte.


#### Le SSD NVMe
![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2269.jpg)
Ici, rien de plus simple que de monter le SSD sur l'adaptateur. J'avais choisi cet adaptateur avec deux slots M.2 et un PCIe x8 sur le dessus, en pensant que la carte mère supportait la bifurcation des lignes PCIe. Malheureusement, cette fonctionnalité n'est disponible que sur les serveurs de gen10 et sur certains serveurs rack de gen9, tels que le DL380 et le DL360.

![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/adaptateur-x4-m2.png)
Je passerai probablement à ce type d'adaptateur à l'avenir, avec 6 slots PCIe, il y a de quoi faire. Cependant, il est dommage de ne pas pouvoir exploiter la totalité des lignes PCIe de chaque slot.
![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2273.jpg)
Ce qui est bien avec ce type de montage, c'est qu'une fois le fan duct installé, un flux d'air frais passera par-dessus.
![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2292.jpg)

##### SSD sata
![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2291.jpg)
J'ai retrouvé deux SSD SATA de 240 Go chacun qui traînaient chez moi. Du coup, je les ai installés, et ils seront configurés en RAID 1 pour y installer l'hyperviseur.
Pour l'instant, ils sont fixés à l'arrache comme ça, mais je vais essayer de trouver une cage pour disque 2,5" ou d'en imprimer une.

#### Caddy pour disque SAS
Passons maintenant aux disques SAS. Le serveur est équipé d'une cage pour 4 disques SAS, mais je n'avais pas les caddies nécessaires pour y installer mes disques. J'ai donc cherché un modèle à imprimer sur [Thingiverse](https://www.thingiverse.com/thing:4577909) et j'ai trouvé celui qui me convenait.

![caddy voron gif][def]

[def]: https://s11.gifyu.com/images/Sguyf.gif

![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2270.jpg)
J'ai dû arrondir un peu les angles pour que ça s'adapte à ma Voron V0.

![](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/IMG_2275.jpg)
Une fois monté, ça ne bouge pas. Cela convient parfaitement, d'autant plus que je n'avais pas spécialement besoin que ce soit hotswappable.
### le test
On démarre le serveur et on se connecte sur l'ILO pour voir si tout est détecté correctement.

- Le CPU
![CPU dans l'ILO](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/chrome_YA2ZhyHUpV.png)

- Les HDD. Il panique parce que les disques ne sont pas d'origine HPE, mais ils fonctionnent. Il ne reste plus qu'à les configurer en RAID 5.
![Disques SAS](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/chrome_dbkC0NaYe5.png)

- Le SSD NVMe
![SSD NVMe](/Articles/2023-08-26-upgrade-cpu,-instalation-des-disques-sas-et-ssd-nvme/chrome_IOFrNHf2ZM.png)

Voilà, maintenant que la partie hardware est finalisée (pour l'instant), il ne reste plus qu'à tout configurer et à installer l'hyperviseur. Nous aborderons cela dans un prochain article.

À bientôt.