# Exercices pratiques de RA avec A-Frame

## Table des matières <!-- omit in toc -->

- [Introduction](#introduction)
- [Installation](#installation)
- [Marche à suivre](#marche-a-suivre)
- [Créer et attribuer un component à une entity](#creer-attribuer)
- [Importer des gltf animés et ajouter des lumières dans une scène](#importer-gltf)
- [Utiliser la fonction `hit-test` de webXR pour drag & drop des objets virtuels](#hit-test)
- [Exemple avancé drag & drop avec `hit-test`](#hit-test-2)
- [Exemple `hit-test` combiné à une interaction sur un objet virtuel](#interact) 


## <a name="introduction">Introduction</a> 
La RA s’appuie essentiellement sur les mêmes concepts techniques que ceux qui ont été introduit lors du cours précédent sur la VR, à savoir le suivi de position (3/6DoF) d’un corps rigide (un périphérique tel qu’une tablette ou des lunettes), lui-même guidé par les mouvements d’un·e utilisateur·ice. Les mêmes typologies de tracking peuvent exister (outside-in ; inside-out), bien que par nature le mode inside-out est bien plus répandu (c’est-à-dire que le suivi de position est réalisé grâce à des données intégrées au périphérique). La différence fondamentale est l’ajout d’une texture «camera» en fond, avec l’ambition que les objets virtuels s’y ancrent de façon réaliste. 

Pour faire de la RA, il faut : 
•	Intégrer un background avec une texture caméra, pour créer la couche «réalité»
•	*n* objets virtuels qui s’ancrent, c-à-d qui interagissent avec cette couche de «réalité» 
•	Un système de suivi positionnel pour compenser et déterminer la position des objets virtuels, afin qu’ils semblent s’ancrer dans la réalité. 

Plusieurs solutions existent, y compris pour le web. Nous allons utiliser ici une approche basée sur l’utilisation du framework A-frame.js, initialement conçu pour la VR, mais qui permet également de faire de la RA. Le framework est basé sur une architecture hiérarchique de type «Entity Component System», normatif dans le monde du jeu vidéo. A-Frame permet de gérer des scènes 3D et les fonctions du mode RA du standard WebXR, qui donne accès aux données de la station inertielle du périphérique ainsi qu’à ses caméras et à la fusion de ces données pour faire du suivi positionnel. Dans les scènes A-frame, des objets sont positionnés selon un système de coordonnées locales, sur une unité métrique (1 = 1 m). La position initiale de la caméra (virtuelle) est l’origine. À mesure que la caméra bouge, elle est trackée et la position de la scène 3D environnante est compensée de manière inversée par rapport au suivi de la caméra: i.e. si le tracking détecte que la caméra s’est déplacée d’1 mètre en avant, A-frame «déplacera» la scène 3D d’1 mètre en arrière. 

## <a name="installation">Installation</a>
Pour accéder facilement à vos projets depuis une tablette, nous allons utiliser le service [glitch](glitch.com) qui permet d’héberger des web apps gratuitement. 

## <a name="marche-a-suivre">Marche à suivre</a>  
1. Créez un compte [glitch](https://glitch.com/) si vous souhaitez sauvegarder vos modifications. 
2. Sur votre laptop, allez sur [glitch](https://glitch.com/edit/#!/sic1-ar). 
3. Cliquez sur «remix» pour créer votre propre copie et éditer le projet. Un nom aléatoire lui sera attribué (vous pouvez le modifier sous «settings»). 
4. Sur la tablette, allez sur https://***-nom-de-votre-projet[.glitch.me](https://glitch.com/edit/#!/***-nom-de-votre-projet) [sur un navigateur supportant webXR](https://caniuse.com/webxr) pour voir le résultat (vous pouvez également visualisez le résultat sur votre laptop, mais la navigation est limitée au clavier WASD + souris). 
5. Entrez en mode «RA» au moyen du bouton en bas à droite. Rafraichissez la page pour voir vos modifications sur le projet. 

[projet RA de base](https://glitch.com/edit/#!/sic1-ar)

## <a name="creer-attribuer">Créer et attribuer un component à une entity (page `index.html`)</a<
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

## <a name="importer-gltf">Importer des gltf animés et ajouter des lumières dans une scène (page `1-assets.html`)</a>
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

## <a name="hit-test">Utiliser la fonction `hit-test` de webXR pour drag & drop des objets virtuels (page `3-drag-simple.html`)</a>
 1. Sur la page `3-drag-simple.html`, ajoutez les attributs suivants à la balise `<a-scene>`:
    `xr-mode-ui="XRMode: ar" webxr="optionalFeatures: hit-test;" ar-hit-test="target:#object;"`
    Ceci permet d’utiliser la fonctionnalité de "hit-test” de l’API WebXR. On précise l’id de la cible que l’on va tester (dont on va tester la collision avec le pointeur, en l‘occurence le doigt sur l’écran tactile, ou la souris). 
 3. Modifiez l’élévation de la caméra d’a-frame à 1.6 m (actuellement à 0.4 m). 
 4. Ajouter l’id “object” à la primitive `<a-sphere>` dans la scène:
    `id="object"`
 5. Testez la fonction “hit-test” en mode RA, sur la tablette. La position de l’objet dans la scène 3D peut désormais être modifiée avec le doigt, en suivant les surfaces détectées par la photogrammétrie de WebXR. 
 6. Extra, si vous êtes rapides. Ajoutez une entity de type "line" à la scène:
    `<a-entity line="start: 0 0 0; end: 0 0 -2; color: red" line__2="start: 0 0 0; end: 0 0 2; color: green"></a-entity>`

## <a name="hit-test-2">Pour un exemple plus avancé de drag & drop avec le `hit-test` de webXR, avec des objets virtuels et leur ombre projetée (page `3-drag.html`)</a>

## <a name="interact">Exemple `hit-test` combiné à une interaction sur un objet virtuel (page `4-interact.html`)</a> 
1. Sur la page `4-interact.html`, créez une entity avec l’`id="camera-rig"` et positionnée à `0 0 0`: 
       ```
       <a-entity
        id="camera-rig"
        position="0 0 0">
        </a-entity>
        ```
2. Dans l’entity que vous venez de créer, créez une entity caméra "enfant" avec l’`id="head"` positionné à 1.6 m de hauteur et attribuez-lui les components `wasd-controls="acceleration: 10"` et `look-controls="pointerLockEnabled: true"`: 
   ```
          <a-camera 
            id="head"     
            position="0 1.6 0"
            wasd-controls="acceleration: 10"
            look-controls="pointerLockEnabled: true">
            <a-entity 
              cursor
              geometry="primitive: circle; radius: 0.0005"
              material="shader: flat; color: black; opacity: 1"
              position="0 0 -0.1"
              visible="true"
              raycaster="far: 10; objects: [cursor-listener], [hover-highlighter]">
            </a-entity>
          </a-camera>
   ```
4. En dessous de l’entity `"camera-rig"`, créez une primitive de type box (sans lien de parenté) avec l’`id="door1"`, une profondeur de 0.1 m, une largeur de 0.85 m, une hauteur de 2 m, et positionnée à `"-0.675 0 0"`. Attribuez-lui une couleur et les components `hover-highlighter`et `cursor-listener`. Attribuez-lui encore le component `toggle-events="sourceEvt: click; evt1: open; evt2: close"` et `emit-when-near="target: #head; distance: 2; event: click; event-far: click;"`. 

```
      <a-box 
      id="door1"
      depth="0.1"
      width="0.85"
      height="2.0"
      position="-0.675 0 0"
      material="color: grey;"
      hover-highlighter
      cursor-listener
      toggle-events="sourceEvt: click; evt1: open; evt2: close"
      emit-when-near="target: #head; distance: 2; event: click; event-far: click;"
      >
      </a-box>
```
 
5. Créez une entity "parente" de cette primitive positionnée à `"0.675 1.25 -4"` et avec les components et attributs suivants: 
   ```
   <a-entity 
    position="0.675 1.25 -4"
    animation__open="startEvents: open; property: rotation.y; to: 90; dur: 2000; easing: linear; loop: false"
    animation__close="startEvents: close; property: rotation.y; to: 0; dur: 2000; easing: linear; loop: false"
    listen-to__open="evt: open; target: #door1; emit: open"
    listen-to__close="evt: close; target: #door1; emit: close">
    ```
6. Testez sur la tablette en mode RA. La porte devrait s’ouvrir si on s’en approche, ou alors si on clique lorsque le `hit-test` est `true` (=lorsque le curseur est sur la porte).
7. Vous pouvez modifier les paramètres du component `animation` pour varier vitesse, angle d’ouverture, etc.

---
[Chapitre suivant : Exercices pratiques de RA avec des géodonnées avec A-Frame](pratique-RA-geo.md)
---
