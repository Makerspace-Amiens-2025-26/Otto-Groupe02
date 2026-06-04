---
layout: default
nav_order: 5
title: Conception et prototypage
---
# Conception et prototypage

## Conception 3D
Nous avons conçu nos pièces personnalisées sur Onshape en partant 
du modèle de base Otto-MKS. Les pièces modifiées sont :
- Le casque et le body inspirés d'un requin
- Les bras articulés reliés par une pelle

![Modèle 3D sur Onshape](images/Skarki1.png)

## Impression 3D
Une fois la conception terminée, nous avons imprimé nos pièces 
sur les imprimantes 3D du MakerSpace.

![Pièces imprimées](images/pieces-imprimees.jpg)

{: .note }
> Mettre ici les paramètres d'impression si vous les avez notés 
> (température, remplissage, supports...)

## Programmation
Nous avons programmé le robot sur Arduino. Le code gère :
- Le déplacement du robot (marche, rotation)
- Le contrôle via l'application RemoteXY en Bluetooth
- Le mode autonome pour avancer sans télécommande
- La gestion des bras pour les attaques

![Interface RemoteXY](images/remotexy.png)
