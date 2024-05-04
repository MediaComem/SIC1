# LBAR.js

## Table des matières <!-- omit in toc -->

- [Introduction](#introduction)
- [Installation](#installation)
- [exemple-1](#exemple-1)
- [exemple-2](#exemple-2)
- [exemple-3](#exemple-3)
  
## <a name="introduction">Introduction</a> 

La réalité augmenté basée sur la géolocalisation se base sur a) des objets virtuels auxquels sont attribuées des coordonnées géographiques b) l’utilisation des données de localisation d’un périphérique pour positionner ces objets virtuels par rapport à l’utilisateur. Afin de pouvoir placer les objets virtuels dans l’interface de RA de façon à ce qu’ils semblent apparaitre à la position réelle souhaitée, il faut:
1.	Récupérer la position géographique du périphérique mobile 
2.	Récupérer l’orientation (absolue) de la caméra du périphérique
3.	Récupérer les autres mesures de pose 6DoF du périphérique (pitch, roll)
4.	Convertir les coordonnées géographiques des objets virtuels en coordonnées locales dans système de référence 3D de la scène

Pour faire cela dans un navigateur web, on peut utiliser [AR.js](https://ar-js-org.github.io/AR.js-Docs/). Pour le faire en profitant de l’écosystème d’A-frame, AR.js dispose d’un component adapté: [aframe-ar.js](https://aframe.io/blog/arjs/). Pour les besoins du projet BiodivAR, nous avons développé notre propre librairie [LBAR.js](https://github.com/MediaComem/LBAR.js/) inspirée d’AR.js. Nous n’avons gardé que la partie relative à la géolocalisation (AR.js permet également de faire du marker-based, etc.), et avons ajouté des fonctions afin de profiter plus largement des capacité de WebXR à faire du tracking basé sur des données fusionnées, et ainsi tenter de limiter le jittering (tremblement) des objets virtuels. 

## <a name="insrallarion">installation</a> 
abc…

## <a name="exemple-1">exemple-1</a> 
## <a name="exemple-2">exemple-2</a> 
## <a name="exemple-3">exemple-3</a> 
