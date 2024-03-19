# Partie 2 - A-Frame VR

## Technologies VR
Les textes et concepts de ce chapitre sont tirés du [cours VR](https://github.com/Chabloz/VR-A-Frame/blob/master/EtatArt.md#hardware) de Nicolas Chabloz.

### Degrés de liberté (DoF)

![Les six degrés de liberté dans un espace à trois dimensions, @author:GregorDS, Wikipedia](./img/6DOF.png)

Les degrés de liberté font référence au nombre et types de mouvements réalisables dans un espace 3D. En réalité virtuelle, il existe différentes possibilités :

Le **3DoF** pour "Three Degrees Of Freedom", qui indique que le matériel peut suivre les mouvements de rotation effectués par l'utilisateur (ces trois axes sont _yaw_, _pitch_ et _roll_).  

Le **6DoF** pour "Six Degrees Of Freedom" qui, en plus de pouvoir suivre les mouvements de rotation, peut également suivre les mouvements de translation (_forward/back_, _up/down_, _left/right_).

En général, le nombre de degrés de liberté du casque correspond à celui du contrôleur (quand il y en a un).

### Suivi de position

Le suivi de position (positional tracking) est possible aujourd'hui avec les 2 techniques ci-dessous :

- Outside-in (Oculus camera IR, Valve lighthouse…)
- Inside-out tracking (simultaneous localization and mapping [SLAM](https://en.wikipedia.org/wiki/Simultaneous_localization_and_mapping))

La première est externe au casque et la seconde est intégrée au casque. Bien que plus intéressante pour sa portabilité, la version _inside-out_ est souvent moins précise et il semble difficile qu'elle offre un jour un tracking du corps entier.

Pour aller plus loin sur leurs fonctionnements : [How VR Positional Tracking System Work](https://uploadvr.com/how-vr-tracking-works)

### Contrôleurs et systèmes d'interaction

La VR se démocratisant, de nouveaux systèmes permettant d'interagir émergent ou se répendent également. Voici quelques systèmes d'interactions possibles aujourd'hui :

**Regard** (_gaze interaction_) : l'utilisateur·trice interagit avec un élément en le centrant dans sa vue (peut-être utiliser avec un minuteur).

**Yeux** (_eye-tracking_) : certains casques permettent de suivre la direction des yeux.

**Contrôleur 3DoF** : suivi des mouvements de rotation uniquement. Son usage en VR se limite à pouvoir pointer des objets comme les télécommandes des TV modernes, mais également à servir aux déplacements. Toutefois, certains contrôleurs peuvent simuler un 6DoF ([Documentation Unity](https://developers.google.com/vr/develop/unity/controller-support)).

**Contrôleur 6DoF** : suivi des mouvement de rotation et de position (dans la main du joueur ou ailleurs). Cela permet d'avoir une immersion quasiment parfaite.

**Mains** : une grande majorité de casques (en 2024), intégrent directement un système de suivi de la position des mains et des doigts de l'utilisateur·trice. Des modèles plus anciens utilisaient des capteurs supplémentaires (comme le [Leap Motion](https://en.wikipedia.org/wiki/Leap_Motion)).

**Visage** : certains casques VR proposent également des fonctions de suivi du visage (_face tracking_) et de ses sous-parties, comme le suivi de la bouche (_mouth/lip tracking_) en autre.

Il existe bien sûr d'autres types de contrôleurs et capteurs plus exotiques, mais ceux-ci reprennent la plupart du temps l'un des principes ci-dessus pour fonctionner.