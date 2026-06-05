---
layout: default
title: Programmation
parent: Etapes de fabrication
nav_order: 4
---
# Programmation

## Outils utilisés
- Arduino IDE
- Application RemoteXY pour le contrôle Bluetooth

## Fonctionnalités programmées

### Déplacement
Le robot peut marcher en avant, en arrière et effectuer 
des rotations grâce aux 4 servomoteurs des jambes.

### Contrôle via RemoteXY
Nous avons créé une interface sur l'application RemoteXY 
qui permet de contrôler le robot en Bluetooth depuis 
un smartphone :
- Joystick de déplacement (avant, arrière, gauche, droite)
- Boutons pour lever et baisser les bras

### Mode autonome
Le robot utilise son capteur ultrason pour détecter 
les obstacles devant lui et avancer seul sans intervention 
humaine. Quand un obstacle est détecté, il s'arrête 
ou change de direction.

### Gestion des bras
Les 2 servomoteurs des bras sont programmés pour lever 
et frapper au moment voulu, dans le but de déséquilibrer 
les robots adverses pendant les épreuves.

## Code source

{: .note }
> Le code source complet est disponible dans le dossier 
> `project/` de notre repo GitHub.
