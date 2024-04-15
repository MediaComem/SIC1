# Bases de A-Frame

A-Frame est un framework web open-source dédié au développement d'expériences en réalité virtuelle et augmentée. Basé sur HTML et Three.js, il permet facilement de créer du contenu en VR.

## Table des matières <!-- omit in toc -->

- [Bases de A-Frame](#bases-de-a-frame)
  - [Prérequis](#prérequis)
  - [Architecture](#architecture)
  - [Entité](#entité)
  - [Scène](#scène)
    - [Support caméra](#support-caméra)
  - [Composant](#composant)
    - [Enregistrer un composant](#enregistrer-un-composant)
  - [Interactions et événements](#interactions-et-événements)
  - [Système](#système)
    - [Enregistrer un système](#enregistrer-un-système)
  - [Gestion d'assets](#gestion-dassets)
  - [Inspecteur visuel](#inspecteur-visuel)

## Prérequis
Afin que vous puissiez suivre les exercices proposés au fur et à mesure, clonez (ou forkez) le repo [boilerplate pour Vue et A-Frame](https://github.com/Meryl-D/a-frame-vite-vue-boilerplate).

## Architecture

A-Frame adopte une architecture Entity-Component-System. Ce modèle se compose des 3 éléments suivants :

- **Entité** : conteneur permettant d'accueillir des composants. Sans composant, les entités ne sont pas rendues, à l'image de `<div>` vides.

- **Composant** : module réutilisable attachable à des entités, pour leur donner une apparence, un comportement ou une fonctionnalité.
- **Système** : module qui permet de gérer des données globales, des services et des classes pour les composants. Si souhaité, il permet également de séparer la logique (système) des données (composant).

Si l'on prend pour exemple un smartphone comme entité, ses composants définiront son apparence (forme, couleur), son comportement (sonner quand on reçoit un appel) et ses fonctionnalités (appel, appareil photo). Le dispositif peut également comprendre un système pour la gestion des comportements liés aux appels.

Plus concrétement, ces éléments sont intégrés comme suit dans A-Frame.

## Entité

La syntaxe de base d'une entité se rapproche d'une balise HTML :

    <a-entity></a-entity>

Il existe cependant des éléments de base préconstruits, appelés _primitives_, qui permettent de construire facilement une scène sans devoir plonger dans l'architecture sous-jacente. On retrouve notamment des éléments tels que `<a-box>`, `<a-sphere>` ou même `<a-sky>`.

Consultez la doc pour parcourir les primitives existantes (en bas du menu) ou créer des [primitives personnalisées](https://aframe.io/docs/1.5.0/introduction/html-and-primitives.html#registering-a-primitive).

## Scène

Une scène est construite avec l'entité `<a-scene>` dans le `<body>` d'une fichier HTML. Elle constitue l'élément racine de toute entité. Elle comprend une caméra `<a-camera>` et des lumières `<a-light>` par défaut, qui sont remplacés si l'on déclare explicitement ces primitives.

On prend, par exemple, une primitive `<a-box>` qui fait apparaître une boîte dans la scène.

    <a-scene>
        <a-box color="red" rotation="0 45 45" scale="2 2 2"></a-box>
    </a-scene>


> [!IMPORTANT]
> Une entité placée à l'intérieur d'une autre hérite de ses transformations (position, scale, rotation, etc.)

Dans l'exemple suivant, la sphère aura la même position, la même rotation et la même échelle que la boîte parente.

    <a-scene>
        <a-box position="0 2 0" rotation="0 45 45" scale="2 4 2">
            <a-sphere></a-sphere>
        </a-box>
    </a-scene>

Le positionnement des entités se fait à partir d'un système de coordonnée basé sur la main droite. Les valeurs positives vont donc à droite (X), en haut (Y) et contre nous (Z).

![Système de coordonnées "main droite", what-when-how.com](../img/326137a8-ab49-11e6-9b76-4e3a65f333d9.jpg)

### Support caméra

Ce système d'héritage requiert ainsi de placer la caméra et les mains dans une sorte de support (camera rig), une entité parente, qui assurera la cohérence dans les déplacements et rotation de ces éléments.

> ### :computer: Exercice
> 
> Insérer une primitive `<a-box>` dans votre scène.
> 
> Note : Sur un ordinateur, déplacez-vous avec les touches `wasd` pour voir le cube.

## Composant

Les composants sont attachés aux entités comme des attributs sur des éléments HTML. Ils peuvent accueillir des données personnalisées sous forme de propriétés.

    <a-box position="0 0 1"></a-box>

Les propriétés multiples sont passées ainsi :

    <a-entity box="width: 2; height: 3"></a-entity>

> [!NOTE]
> Les unités sont en mètres.

> ### :computer: Exercice
> 
> Modifiez la position de la box placée précédemment pour qu'elle apparaisse devant vous (au chargement). Ajoutez lui également une couleur de votre choix.
> 

### Enregistrer un composant

Comme pour les primitives, il est possible de créer nous-même des composants avec la fonction `registerComponent`.


        AFRAME.registerComponent('log', {
            schema: {
                message: { 
                    type: 'string',
                    default: 'Hello, World!'
                    }
            },
            init: function () {
                console.log(this.data.message)
            },
            update: function (oldData) {...}

        })

Une fois enregistré le composant peut être utilisé.

    <a-entity log="message: Hello everyone!"></a-entity>

On utilise un `schema` pour enregistrer des propriétés qui peuvent être transmise par l'entité. Dans le composant, on accède aux propriétés via `this.data`.

La fonction `init` est appelée une seule fois, lors de l'initialisation du composant. Si on veut changer dynamiquement les propriétés passées, on peut mettre à jour le composant avec la fonction `update`.

Les [composants](https://aframe.io/docs/1.5.0/core/component.html) offrent également d'autres fonctions utilitaires, comme `tick()`, `play()` ou `pause()`. 

> [!TIP]
> Pas besoin de réinventer la roue ! Il existe de multiples composants natifs à A-Frame et bien d'autres ont déjà été créés par la communauté, comme [a-frame-extras](https://github.com/c-frame/aframe-extras) et [physx](https://github.com/c-frame/physx).

> ### :computer: Exercice
> 
> Créer un nouveau composant nommé `clickable` dans le dossier `aframe`. Celui-ci permettera d'indiquer visuellement si un élément peut être "cliqué".
>
> Dans `schema`, ajouter une propriété `color` de type `color` et la couleur par défaut de votre choix (différente de celle du cube).
>
> Avant de continuer, il faut comprendre comment fonctionne les interactions et les événements dans A-Frame.
>

## Interactions et événements

Quand on manipule les différentes entités, il est préférable de le faire dans un composant. Comme A-Frame est basé sur HTML, on peut accéder aux entités en utilisant des méthode de sélection comme `.querySelector()` par exemple.

> [!TIP]
> À l'intérieur d'un composant, on a accès directement à l'entité liée via `this.el` et à la scène via `this.el.sceneEl`.

    let boxes = this.el.sceneEl.querySelectorAll('a-box');

Pour récupérer les valeurs des propriétés d'un composant lié à une entité, on utilise la méthode `.getAttribute()`.

    el.getAttribute('width')

Pour attacher un composant à une entité ou le mettre à jour, on utilise la méthode `.setAttribute()`.

    el.setAttribute('geometry', {
        primitive: 'box',
        height: 3,
        width: 1
    });

> [!IMPORTANT]
> Pour des raisons de performances, il est préférable de mettre à jour les valeurs de position, rotation, échelle (scale) et visibilité (visible) via three.js directement.

    el.object3D.position.x += 5

En ce qui concerne les événements, on peut en émettre avec la fonction `.emit()` et en écouter avec `addEventListener()`.

    el.emit('physicscollided', {collidingEntity: anotherEl}, false);

    el.addEventListener('physicscollided', event => {
        console.log('Entity collided with', event.detail.collidingEntity);
    });

> ### :computer: Exercice
> 
> Reprenons notre composant `clickable`. Nous allons créer 2 fonctions `onEnter` et `onLeave`, qui seront respectivement appelées lors des événements `mouseenter` et `mouseleave`. Dans la fonction `init`, nous allons lier (bind) les événements au composant pour pouvoir utiliser `this`. Cela peut être fait ainsi (à faire également pour onLeave) :

    this.onEnter = this.onEnter.bind(this);
    this.el.addEventListener('mouseenter', this.onEnter);

> Dans la fonction onEnter, on va vérifier si on utilise le raycaster ou le curseur (VR ou 3D) pour ensuite changer la couleur.
>
    onEnter: function (evt) {
        const cursor = evt.detail.cursorEl;
        if (cursor.getAttribute('raycaster').showLine) {
        this.savedColor = cursor.getAttribute('raycaster').lineColor;
        cursor.setAttribute('raycaster', 'lineColor', this.data.color);
        } else {
        this.savedColor = cursor.getAttribute('material').color;
        cursor.setAttribute('material', 'color', this.data.color);
        }
    },

> En se basant sur la fonction `onEnter`, écrivez le contenu de `onLeave` pour revenir à la couleur de base.
>
> Enfin, pour des raisons de performences, on supprime les `eventListeners` lorsque l'entité est enlevée de la scène.
>
    remove: function () {
        this.el.removeEventListener('mouseenter', this.onEnter);
        this.el.removeEventListener('mouseleave', this.onLeave);
    },

> Vous pouvez tester votre composant en l'attachant au cube présent dans votre scène.


## Système

On peut accéder à un système via la scène :

    document.querySelector('a-scene').systems[systemName];

### Enregistrer un système

Un système est enregistré de façon similaire à un composant. Si le nom du système correspond au nom du composant, on peut y accéder directement depuis le composant via `this.system`.

    AFRAME.registerSystem('camera', {
        schema: {},
        init: function () {},
        ...
    });

    AFRAME.registerComponent('camera', {
        schema: {...},
        init: function () {
            console.log(this.system)
        },
        ...
    });

## Gestion d'assets

A-Frame offre la possibilité de charger différents assets, comme du son, des images, des vidéos, des matériaux et des modèles 3D.

Le framework possède un système de gestion d'assets, qui permet de regrouper, pré-charger et mettre en cache les assets, ce qui assure de meilleures performance.

Afin d'utiliser ce système, les assets sont placés dans une entité `<a-assets>`.

    <a-scene>

        <!-- Système de gestion d'assets -->
        <a-assets>
            <!-- Modèle 3D au format GLTF -->
            <a-asset-item id="horse-gltf" src="horse.gltf"></a-asset-item>

            <audio id="neigh" src="neigh.mp3"></audio>
            <img id="advertisement" src="ad.png">
            <video id="kentucky-derby" src="derby.mp4"></video>
        </a-assets>

        <!-- Scene -->
        <a-entity gltf-model="#horse-gltf"></a-entity>
        <a-sound src="#neigh"></a-sound>
        <a-plane src="#advertisement"></a-plane>
        <a-entity geometry="primitive: plane" material="src: #kentucky-derby"></a-entity>

    </a-scene>

## Inspecteur visuel

A-Frame offre un outil de visualisation, qui permet de voir sa scène sous d'autres angles et de faciliter ainsi le développement. Il peut être ouvert via la commande `ctrl + alt + i`.

![inspecteur visuel d'A-Frame, A-Frame](../img/visual-inspector-aframe.png)

---

[Chapitre précédant : Introduction à la VR](Introduction-VR.md)\
[Chapitre suivant : Exercice pratique VR](Pratique-VR.md)
