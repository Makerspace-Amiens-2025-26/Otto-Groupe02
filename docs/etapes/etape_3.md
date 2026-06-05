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

(Pour voir où elle se situe regarder les images de l'onglets d'avant)

### Servomoteurs des bras
Les 2 servomoteurs supplémentaires pour les bras sont 
branchés sur les pins restants de l'ESP32-XIAO. C'est à dire sur les pins D3 et D6.

<img width="768" height="1024" alt="3D2B6E5C-959D-4A4E-8B84-3779260FA047_1_105_c" src="https://github.com/user-attachments/assets/739b80f8-8bab-4575-a909-872b75c08ade" />

<img width="768" height="1024" alt="B4B7802A-5F1C-4612-83B6-A3DAF85D63A0_1_105_c" src="https://github.com/user-attachments/assets/1bca4c68-a6d8-425c-b53e-0215ed1557fe" />

### Capteur ultrason
Le capteur ultrason est branché sur l'ESP32-XIAO pour 
détecter les obstacles en mode autonome.


