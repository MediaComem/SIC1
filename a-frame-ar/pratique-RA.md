# Exercices pratiques de RA avec A-Frame

## Introduction 
La RA s’appuie essentiellement sur les mêmes concepts techniques que ceux qui ont été introduit lors du cours précédent sur la VR, à savoir le suivi de position (3/6DoF) d’un corps rigide (un périphérique tel qu’une tablette ou des lunettes), lui-même guidé par les mouvements d’un·e utilisateur·ice. Les mêmes typologies de tracking peuvent exister (outside-in ; inside-out), bien que par nature le mode inside-out est bien plus répandu (c’est-à-dire que le suivi de position est réalisé grâce à des données intégrées au périphérique). La différence fondamentale est l’ajout d’une texture «camera» en fond, avec l’ambition que les objets virtuels s’y ancrent de façon réaliste. 

Pour faire de la RA, il faut : 
•	Intégrer un background avec une texture caméra, pour créer la couche «réalité»
•	*n* objets virtuels qui s’ancrent, c-à-d qui interagissent avec cette couche de «réalité» 
•	Un système de suivi positionnel pour compenser et déterminer la position des objets virtuels, afin qu’ils semblent s’ancrer dans la réalité. 

Plusieurs solutions existent, y compris pour le web. Nous allons utiliser ici une approche basée sur l’utilisation du framework A-frame.js, initialement conçu pour la VR, mais qui permet également de faire de la RA. Le framework est basé sur une architecture hiérarchique de type «Entity Component System», normatif dans le monde du jeu vidéo. A-Frame permet de gérer des scènes 3D et les fonctions du mode RA du standard WebXR, qui donne accès aux données de la station inertielle du périphérique ainsi qu’à ses caméras et à la fusion de ces données pour faire du suivi positionnel. Dans les scènes A-frame, des objets sont positionnés selon un système de coordonnées locales, sur une unité métrique (1 = 1 m). La position initiale de la caméra (virtuelle) est l’origine. À mesure que la caméra bouge, elle est trackée et la position de la scène 3D environnante est compensée de manière inversée par rapport au suivi de la caméra: i.e. si le tracking détecte que la caméra s’est déplacée d’1 mètre en avant, A-frame «déplacera» la scène 3D d’1 mètre en arrière. 

## Installation 
Pour accéder facilement à vos projets depuis une tablette, nous allons utiliser le service [glitch](glitch.com) qui permet d’héberger des web apps gratuitement. 

## Marche à suivre:  
1. Créez un compte [glitch](https://glitch.com/) si vous souhaitez sauvegarder vos modifications. 
2. Sur votre laptop, allez sur [glitch](https://glitch.com/edit/#!/sic1-ar). 
3. Cliquez sur «remix» pour créer votre propre copie et éditer le projet. Un nom aléatoire lui sera attribué (vous pouvez le modifier sous «settings»). 
4. Sur la tablette, allez sur [https://***-nom-de-votre-projet.glitch.me](https://glitch.com/edit/#!/***-nom-de-votre-projet) [sur un navigateur supportant webXR](https://caniuse.com/webxr) pour voir le résultat (vous pouvez également visualisez le résultat sur votre laptop, mais la navigation est limitée au clavier WASD + souris). 
5. Entrez en mode «RA» au moyen du bouton en bas à droite. Rafraichissez la page pour voir vos modifications sur le projet. 

[projet RA de base](https://glitch.com/edit/#!/sic1-ar)

## Créer et attribuer un component à entité (page `index.html`) 
Pour commencer, remarquez que la scene A-frame ne comporte rien de particulier. La balise `<a-scene>` a un attribut `vr-mode-ui="enterAREnabled: true"` pour afficher le bouton entrer en RA, mais celui-ci s’affiche par défaut sur les périphériques capables de RA, il n’est donc pas essentiel. La camera n’a pas de `rig` ni de controllers. Remarquez enfin le component `hide-on-enter-ar` attribué à l’entity `<a-sky>`: cette entité n’est plus visible une fois le mode RA activé.

1. Créez un nouveau fichier nommé `animate-scale.js` dans le directoire `js`. Collez-y le code suivant:

```
AFRAME.registerComponent('animate-scale', {
    multiple: true,
    schema: {
        factor: {type: 'number', default: 1},
        axe: {type: 'string', default: 'x'},
        active: {type: 'int', default: 1}

    },
    init: function () {
        
    },

    remove: function () {
    },

    update: function () {
    },

    tick: function (elapsed) {
        
        if (this.data.active===1){
            this.el.object3D.scale[this.data.axe] = 1 + (this.data.factor) * ((Math.sin((elapsed) / 200)) / 10);
        }
        
    }
})
```
2. Sur la page `index.html`, ajouter une ligne pour importer le component que vous avez créé, dans <head>: 
   `<script src="js/animate-scale.js"></script>`
3. Choisissez une des entity de la a-scene et ajoutez-lui le component:
   `animate-scale="factor:.3; axe:x; active:1"`
4. Vous pouvez ajouter plusieurs instances du même component (en l’occurence, un par axe). Il faut ajouter "__" et un nom unique à chaque instance:
   ```
    animate-scale__="factor:.3; axe:x"
    animate-scale__lalala="factor:.3; axe:y"
    animate-scale__123581321="factor:.3; axe:z"
   ```

## Importer des gltf animés et ajouter des lumières dans une scène (page `1-assets.html`) 
1. Sur la page `1-assets.html`, en tant qu’enfant de l’entity qui porte l’`id="objet"`, ajoutez une entity qui contient un `gltf` statique:
`<a-entity position="0 0 -1" shadow gltf-model="url(https://cdn.glitch.global/36ffed16-a93a-4d6b-a6cd-669447f2a1e9/Triceratops-100k-2048_std_draco.glb?v=1713795783826)"></a-entity>`
2. Deuxièmement, importez la libairie `a-frame-extras`, qui permet notamment de jouer les animations contenues dans des `gltf`:
`<script src="https://cdn.jsdelivr.net/gh/c-frame/aframe-extras@7.4.0/dist/aframe-extras.min.js"></script>`
3. Importez l’asset suivant au debut de votre scène, entre des balises `<a-assets>…</a-assets>`:
```
  <a-assets>
    <a-asset-item id="biche" src="https://cdn.glitch.global/36ffed16-a93a-4d6b-a6cd-669447f2a1e9/biche.glb?v=1713793197942"></a-asset-item>
  </a-assets>
```
Notez que cette action n’a pas créé d’entity dans la scène, cela a uniquement permis d’importer une ressource et de garantir son chargement avant le chargement du reste de la page. 
4. Ajoutez l’entity suivante en tant qu’enfant de l’entity qui porte l’`id="objet"`: 
`<a-entity gltf-model="#biche" id="directionaltarget" position="-2 0 -1"></a-entity>`
5. Ajoutez le component `animation-mixer` à cette entity pour jouer l’animation contenue dans le gltf:
6. Ajoutez une entity lumière `<a-light>` à la scène:
`<a-light type="directional" position="0 0 0" intensity="3.0" target="#directionaltarget"></a-light>`

## Utiliser la fonction `hit-test` de webXR pour drag & drop des objets virtuels (page `3-drag-simple.html`) 
 1. Sur la page `3-drag-simple.html`, ajoutez les attributs suivants à la balise `<a-scene>`:
    `xr-mode-ui="XRMode: ar" webxr="optionalFeatures:  hit-test;" ar-hit-test="target:#objects;"`
 2. Paramétrez l’élévation de la caméra d’a-frame à 1.6 m(actuellement à 0.4 m). 
 3. 


LBAR.js

Dans notre cas, nous souhaitons faire de la RA géolocalisée : Les objets virtuels dont nous disposons ont des attributs de pose exprimés en coordonnées géographiques. Afin de pouvoir placer ces éléments par rapport à l’interface utilisateur du périphérique, il faut :
1.	Récupérer la position géographique du périphérique mobile 
2.	Récupérer l’orientation (absolue) de la caméra du périphérique
3.	Récupérer les autres mesures de pose 6DoF de la caméra (pitch, roll)
4.	Convertir les coordonnées géographiques des objets virtuels en coordonnées locales, par rapport à l’origine (la caméra virtuelle), d’après ses données de pose 

<>

Pour cela, nous allons utiliser une librairie que nous avons développée au MEI pour intégrer de la géoinformation dans A-Frame : LBAR.js (pour Location-based Augmented Reality) <https://github.com/MediaComem/LBAR.js/>
