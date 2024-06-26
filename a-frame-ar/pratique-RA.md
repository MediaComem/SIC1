# Exercices pratiques de RA avec A-Frame

## Table des matières <!-- omit in toc -->

- [1. Introduction](#introduction)
- [2. Installation](#installation)
- [3. Marche à suivre](#marche-a-suivre)
- [4. Créer et attribuer un component à une entity](#creer-attribuer)
- [5. Importer des gltf animés et ajouter des lumières dans une scène](#importer-gltf)
- [6. Utiliser la fonction `hit-test` de webXR pour drag & drop des objets virtuels](#hit-test)
- [7. Exemple avancé drag & drop avec `hit-test`](#hit-test-2)
- [8. Exemple `hit-test` combiné à une interaction sur un objet virtuel](#interact) 
- [9. Exemple marker-based AR avec `AR.js`](#marker-based) 
---
## <a name="introduction">1. Introduction</a> 
La RA s’appuie essentiellement sur les mêmes concepts techniques que ceux qui ont été introduit lors du cours précédent sur la VR, à savoir le suivi de position d’un corps rigide (un périphérique tel qu’une tablette ou des lunettes), mais là où 3 degrés de liberté peuvent suffire à faire de la VR, il en faut 6 en RA. Les mêmes méthodes de tracking peuvent exister (outside-in ; inside-out), bien que le mode inside-out est bien plus pratique et répandu (c’est-à-dire que le suivi de position est réalisé grâce à des données intégrées au périphérique). Une différence fondamentale est l’utilisation d’un «fond camera» et l’ancrage d’objets virtuels de façon réaliste. Pour faire tout cela sur un navigateur web, l’API WebXR du W3C repose sur WebGL et permet notamment de gérer l’accès aux données des capteurs du périphérique utilisé (IMU, GNSS).

Pour faire de la RA, il faut : 
- Intégrer un background avec une texture caméra, pour créer la couche «réalité»
- *n* objets virtuels qui s’ancrent, c-à-d qui interagissent avec cette couche de «réalité» 
- Un système de suivi positionnel pour compenser et déterminer la position des objets virtuels, afin d’entretenir l’illusion que les objets virtuels s’ancrent à la réalité. 

Plusieurs solutions existent, y compris pour le web [wiki: "AR"](https://aframe.wiki/en/#!pages/ar.md). Nous allons utiliser ici le framework A-frame.js avec le component [`aframe-ar.js`](https://github.com/chenzlabs/aframe-ar). A-Frame permet de gérer des scènes 3D et aframe-ar.js ([documentation](https://ar-js-org.github.io/AR.js-Docs/)) un mode RA basé sur l’utilisation des données de la station inertielle du périphérique ainsi qu’à ses caméras. Dans les scènes A-frame, des objets sont positionnés selon un système de coordonnées locales, sur une unité métrique (1 = 1 m). La position initiale de la caméra (virtuelle) est l’origine. À mesure que le periphérique bouge, il est «tracké» et la position de la caméra virtuelle mise à jour pour correspondre au mouvement mesuré. I.e. si le tracking détecte que le périphérique s’est déplacé d’1 mètre en avant, A-frame déplacera la scène 3D d’1 mètre en arrière pour faire correspondre virtuel et réel. 

---
## <a name="installation">2. Installation</a>
Pour accéder facilement à vos projets depuis une tablette (RA mobile), nous allons utiliser le service [glitch](glitch.com) qui permet d’héberger des web apps gratuitement. Pour faire de la RA mobile avec glitch, A-frame.js et ar.js, il faut importer A-frame: 
```
<script src="https://aframe.io/releases/1.4.0/aframe.min.js"></script>
```
ainsi que le component [`aframe-ar.js`](https://github.com/chenzlabs/aframe-ar):

```
<script src="https://ghcdn.rawgit.org/chenzlabs/aframe-ar/8a7ee3b/dist/aframe-ar.min.js"></script>
```
Nous allons compléter quelques exemples ensemble pour s’accoutumer avec les concepts d’A-frame et d’AR.js: [exercices pratiques RA mobile](https://glitch.com/edit/#!/sic1-ar). 

---
## <a name="marche-a-suivre">3. Marche à suivre</a>  
1. Créez un compte [glitch](https://glitch.com/) si vous souhaitez sauvegarder vos modifications. 
2. Sur votre laptop, allez sur le [projet d’exercice](https://glitch.com/edit/#!/sic1-ar). 
3. Cliquez sur «remix» pour créer votre propre copie et éditer le projet. Un nom aléatoire lui sera attribué (vous pouvez le modifier sous «settings»). 
4. Sur la tablette, allez sur https://***-nom-de-votre-projet[.glitch.me](https://glitch.com/edit/#!/***-nom-de-votre-projet) [sur un navigateur supportant webXR](https://caniuse.com/webxr) pour voir le résultat (vous pouvez également visualisez le résultat sur votre laptop, mais la navigation est limitée au clavier WASD + souris). 
5. Entrez en mode «RA» au moyen du bouton en bas à droite. Rafraichissez la page pour voir vos modifications sur le projet. 

---
## <a name="creer-attribuer">4. Créer et attribuer un component à une entity (page `index.html`)</a<
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
   ```<script src="js/animate-scale.js"></script>```
3. Choisissez une des entity de la a-scene et ajoutez-lui le component:
   ```animate-scale="factor:.3; axe:x; active:1"```
4. Vous pouvez ajouter plusieurs instances du même component (en l’occurence, un par axe). Il faut ajouter "__" et un nom unique à chaque instance:
   ```
    animate-scale__="factor:.3; axe:x"
    animate-scale__lalala="factor:.3; axe:y"
    animate-scale__123581321="factor:.3; axe:z"
   ```
---
## <a name="importer-gltf">5. Importer des gltf animés et ajouter des lumières dans une scène (page `1-assets.html`)</a>
1. Sur la page `1-assets.html`, en tant qu’enfant de l’entity qui porte l’`id="objet"`, ajoutez une entity qui contient un `gltf` statique:
```<a-entity position="0 0 -1" shadow gltf-model="#biche"></a-entity>```
2. Deuxièmement, importez la libairie `a-frame-extras`, qui permet notamment de jouer les animations contenues dans des `gltf`:
```<script src="https://cdn.jsdelivr.net/gh/c-frame/aframe-extras@7.4.0/dist/aframe-extras.min.js"></script>```
3. Importez l’asset suivant au debut de votre scène, entre des balises `<a-assets>…</a-assets>`:
```
  <a-assets>
    <a-asset-item id="biche" src="https://cdn.glitch.global/36ffed16-a93a-4d6b-a6cd-669447f2a1e9/biche.glb?v=1713793197942"></a-asset-item>
  </a-assets>
```
Notez que cette action n’a pas créé d’entity dans la scène, cela a uniquement permis d’importer une ressource et de garantir son chargement avant le chargement du reste de la page. 

4. Ajoutez l’entity suivante en tant qu’enfant de l’entity qui porte l’`id="objet"`: 
```<a-entity gltf-model="#biche" id="directionaltarget" position="-2 0 -1"></a-entity>```
5. Ajoutez le component `animation-mixer` à cette entity pour jouer l’animation contenue dans le gltf:
6. Ajoutez une entity lumière `<a-light>` à la scène:
```
<a-light type="directional" position="0 0 0" intensity="3.0" target="#directionaltarget"></a-light>
```
---
## <a name="hit-test">6. Utiliser la fonction `hit-test` de webXR pour drag & drop des objets virtuels (page `3-drag-simple.html`)</a>
 1. Sur la page `3-drag-simple.html`, ajoutez les attributs suivants à la balise `<a-scene>`:
    ```xr-mode-ui="XRMode: ar" webxr="optionalFeatures: hit-test;" ar-hit-test="target:#object;"```
    Ceci permet d’utiliser la fonctionnalité de "hit-test” de l’API WebXR. On précise l’id de la cible que l’on va tester (dont on va tester la collision avec le pointeur, en l‘occurence le doigt sur l’écran tactile, ou la souris). 
 3. Modifiez l’élévation de la caméra d’a-frame à 1.6 m (actuellement à 0.4 m). 
 4. Ajouter l’id “object” à la primitive `<a-sphere>` dans la scène:
    ```id="object"```
 5. Testez la fonction “hit-test” en mode RA, sur la tablette. La position de l’objet dans la scène 3D peut désormais être modifiée avec le doigt, en suivant les surfaces détectées par la photogrammétrie de WebXR. 
 6. Extra, si vous êtes rapides. Ajoutez une entity de type "line" à la scène:
    ```
    <a-entity line="start: 0 0 0; end: 0 0 -2; color: red" line__2="start: 0 0 0; end: 0 0 2; color: green"></a-entity>
    ```
---
## <a name="hit-test-2">7. Pour un exemple plus avancé de drag & drop avec le `hit-test` de webXR, avec des objets virtuels et leur ombre projetée (page `3-drag.html`)</a>
---
## <a name="interact">8. Exemple `hit-test` combiné à une interaction sur un objet virtuel (page `4-interact.html`)</a> 
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
## <a name="marker-based">9. Exemple marker-based AR avec `AR.js` (page `5-marker-based.html`)</a> 
1. Sur la page `5-marker-based.html`, importez la librairie `aframe-ar.js` dans `<head>`:
   ```
   <script src="https://jeromeetienne.github.io/AR.js/aframe/build/aframe-ar.js"></script>
   ```
2. Sur la balise `<a-scene>`, ajoutez les attributs suivants `embedded arjs`.
3. Dans la scène, créez une entity primitive rouge de type sphère à l’échelle 0.5 avec une opacité de moitié:
   ```
    <a-entity>
        <a-sphere scale="0.5 0.5 0.5" color="red" opacity="0.5"></a-sphere>
    </a-entity>
   ```
4. Créez un objet camera avec la syntaxe ci-après:
```
<a-marker-camera preset="hiro"></a-marker-camera>
```
5. La caméra virtuelle se déplace désormais par rapport à un marqueur visuel. Ouvrez le projet sur une tablette ou un mobile et placez le marqueur visuel fourni (imprimé sur papier A4) dans le champ de la caméra.
6. Le marqueur est disponible [ici](https://github.com/jeromeetienne/AR.js/blob/master/data/images/HIRO.jpg). Il est possible d’en créer de nouveau [ici](https://jeromeetienne.github.io/AR.js/three.js/examples/marker-training/examples/generator.html). Il est aussi possible de gérer plusieurs marqueurs visuels et de suivre leurs positions. Consultez la [documentation](https://aframe.io/blog/arjs/#how-to-handle-multiple-distinct-markers).
   
---
[Chapitre suivant : Exercices pratiques de RA avec des géodonnées avec A-Frame](pratique-RA-geo.md)
---
