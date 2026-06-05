---
layout: default
title: Câblage électronique
parent: Etapes de fabrication
nav_order: 3
---
# Câblage électronique

## Composants nécessaires
- 1x microcontrôleur ESP32-XIAO
- 6x servomoteurs DF9GMS
- 1x capteur ultrason
- 1x batterie 9V
- Câbles de connexion

## Branchements

### Servomoteurs des jambes
Les 4 servomoteurs des jambes sont branchés sur les pins 
de l'ESP32-XIAO (les pins : D10,D9,D8,D7) :
- Hanche droite
- Hanche gauche  
- Pied droit
- Pied gauche

### Servomoteurs des bras
Les 2 servomoteurs supplémentaires pour les bras sont 
branchés sur les pins restants de l'ESP32-XIAO. C'est à dire sur les pins D3 et D6.

### Capteur ultrason
Le capteur ultrason est branché sur l'ESP32-XIAO pour 
détecter les obstacles en mode autonome.


