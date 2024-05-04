# LBAR.js

## Table des matières <!-- omit in toc -->

- [Introduction](#introduction)
- [Installation](#installation)
- [Positionner un objet virtuel avec des coordonnées géographiques avec `LBAR.js`](#exemple-1)
  
## <a name="introduction">Introduction</a> 

La réalité augmenté basée sur la géolocalisation se base sur a) des objets virtuels auxquels sont attribuées des coordonnées géographiques b) l’utilisation des données de localisation d’un périphérique pour positionner ces objets virtuels par rapport à l’utilisateur. Afin de pouvoir placer les objets virtuels dans l’interface de RA de façon à ce qu’ils semblent apparaitre à la position réelle souhaitée, il faut:
1.	Récupérer la position géographique du périphérique mobile 
2.	Récupérer l’orientation (absolue) de la caméra du périphérique
3.	Récupérer les autres mesures de pose 6DoF du périphérique (pitch, roll)
4.	Convertir les coordonnées géographiques des objets virtuels en coordonnées locales dans système de référence 3D de la scène

Pour faire cela dans un navigateur web, on peut utiliser [AR.js](https://ar-js-org.github.io/AR.js-Docs/). Pour le faire en profitant de l’écosystème d’A-frame, AR.js dispose d’un component adapté: [aframe-ar.js](https://aframe.io/blog/arjs/). Pour les besoins du projet BiodivAR, nous avons développé notre propre librairie [LBAR.js](https://github.com/MediaComem/LBAR.js/) inspirée d’AR.js. Nous n’avons gardé que la partie relative à la géolocalisation (AR.js permet également de faire du marker-based, etc.), et avons ajouté des fonctions afin de profiter plus largement des capacité de WebXR à faire du tracking basé sur des données fusionnées, et ainsi tenter de limiter le jittering (tremblement) des objets virtuels. 

## <a name="insrallarion">Installation</a> 
- Pour commencer, rendez-vous sur l’url [https://glitch.com/edit/#!/lbar-js](https://glitch.com/edit/#!/lbar-js) et créez un clone ("remix") du projet. Pour utilisez [LBAR.js] dans A-frame, importez la librairie dans la `<head>`:
  ```<script src="https://raw.githack.com/MediaComem/LBAR.js/main/dist/lbar-v0.3.1.min.js"></script>```

## <a name="exemple-1">Positionner un objet virtuel avec des coordonnées géographiques avec [`LBAR.js`](https://github.com/MediaComem/LBAR.js/)</a> 
1. Sur la page `index.html`, importez la librairie [´LBAR.js´](https://github.com/MediaComem/LBAR.js/) dans la `<head>`:
```<script src="https://raw.githack.com/MediaComem/LBAR.js/main/dist/lbar-v0.3.1.min.js"></script>```
2. Ajoutez à la balise `<a-scene>` le système "`gps-position`" avec les attributs suivants:
```gps-position webxr="referenceSpaceType: unbounded; requiredFeatures: unbounded;"```
3. Dans la scène, créez une nouvelle entity avec le component "`faces-north`":
```<a-entity faces-north></a-entity>```
4. Créez une entity primitive de l’entity précédente avec le component "`gps-position`" afin d’attribuer des coordonnées géographiques à cette entity. Insérez des coordonnées à votre proximité immédiate afin de pouvoir tester le résultat… 
```<a-box gps-position="latitude: XX.XXXXXXX; longitude: YY.YYYYYYY" color="red"></a-box>```
5. Allez dans les environs des coordonnées que vous avez choisies. Ouvrez la page sur la tablette 4G et cherchez votre objet virtuel…
