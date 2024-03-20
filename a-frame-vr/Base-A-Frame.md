# Bases de A-Frame

A-Frame est un framework web open-source dédié au développement d'expériences en réalité virtuelle et augmentée. Basé sur HTML et Three.js, il permet facilement de créer du contenu en VR.

## Architecture

A-Frame adopte une architecture Entity-Component-System. Ce modèle se compose des 3 éléments suivants :

- **Entité** : conteneur permettant d'accueillir des composants. Sans composant, les entités ne sont pas rendues, à l'image de `<div>` vides.

- **Composant** : module réutilisable attachable à des entités, pour leur donner une apparence, un comportement ou une fonctionnalité.
- **Système** : module qui permet de gérer des données globales, des services et des classes pour les composants. Si souhaité, il permet également de séparer la logique (système) des données (composant).

Si l'on prend pour exemple un smartphone comme entité, ses composants définiront son apparence (forme, couleur), son comportement (sonner quand on reçoit un appel) et ses fonctionnalités (appel, appareil photo). Le dispositif peut également comprendre un système pour la gestion des comportements liés aux appels.

Plus concrétement, ces éléments sont intégrer comme suit dans A-Frame.

## Entité

La syntaxe de base d'une entité se rapproche d'une balise HTML :

    <a-entity></a-entity>

Il existe cependant des éléments de base préconstruits, appelés _primitives_, qui permettent de construire facilement une scène sans devoir plonger dans l'architecture sous-jacente. On retrouve notamment des éléments tels que `a-box`, `a-sphere` ou même `a-sky`.

Consultez la doc pour parcourir les primitives existantes (en bas du menu) ou créer des [primitives personnalisées](https://aframe.io/docs/1.5.0/introduction/html-and-primitives.html#registering-a-primitive).

## Composant

Les composants sont attachés aux entités comme des attributs sur des éléments HTML.

    <a-entity position="0 0 1"></a-entity>

Selon leur nature, ils peuvent accueillir des données sous forme de propriétés.

### Enregistrer d'un composant

Afin d'illustrer les différentes partie d'un composant et leurs fonctions, on va créer un composant `box`.

Dans un premier temps, on enregistre un composant avec la fonction suivante :

        AFRAME.registerComponent('box', {
            // code
        })

On utilise un schema pour enregistrer des propriétés qui peuvent être transmise par l'entité. 

> [!NOTE]
> Les unités sont en mètres.

    AFRAME.registerComponent('box', {
        schema: {
            width: {type: 'number', default: 1},
            height: {type: 'number', default: 1},
            depth: {type: 'number', default: 1},
            color: {type: 'color', default: '#AAA'}
        },
    });

Les propriétés multiples sont passées ainsi :

    <a-entity box="width: 2; height: 3"></a-entity>

La fonction init est appelée une seule fois, lors de l'initialisation du composant. Pour `box`, l'objet 3D est créé avec three.js. Consultez le [manuel de three.js](https://threejs.org/manual/#en/primitives) pour en apprendre plus.

        AFRAME.registerComponent('box', {
            schema: {...},

            init: function () {
                let data = this.data; // propriétés
                let el = this.el; // entité liée

                this.geometry = new THREE.BoxGeometry(data.width, data.height, data.depth);
                this.material = new THREE.MeshStandardMaterial({color: data.color});
                this.mesh = new THREE.Mesh(this.geometry, this.material);

                el.setObject3D('mesh', this.mesh);
            },
        });

La fonction update est appelée à l'initialisation et quand les propriétés changent. Dans le cas de `box`, on met à jour le mesh de l'objet.

        AFRAME.registerComponent('box', {
            schema: {...},

            init: function () {...},

            update: function (oldData) {
                let data = this.data;
                let el = this.el;

                // Si `oldData` est vide, cela signifie qu'on est dans la phase d'initialisation, et qu'il n'y a donc rien à mettre à jour. 
                if (Object.keys(oldData).length === 0) { return; }

                // Mise à jour de la géométrie lors du changement des propriétés
                if (data.width !== oldData.width ||
                    data.height !== oldData.height ||
                    data.depth !== oldData.depth) {
                el.getObject3D('mesh').geometry = new THREE.BoxGeometry(data.width, data.height, data.depth);
                }

                // Mise à jour du matériau
                if (data.color !== oldData.color) {
                el.getObject3D('mesh').material.color = new THREE.Color(data.color);
                }
            }
        });

Afin d'optimiser le rendu de la scène, il est nécessaire de supprimer le mesh quand l'entité est supprimée.

    AFRAME.registerComponent('box', {
        schema: {...},

        init: function () {...},
        update: function (oldData) {...},

        remove: function () {
            this.el.removeObject3D('mesh');
        }
    });

Le composant `box` est terminé ! Les [composants](https://aframe.io/docs/1.5.0/core/component.html) offrent d'autres fonctions utilitaires, comme `tick()`, `play()` ou `pause()`.

## Système

On peut accéder à un système via la scène :

    document.querySelector('a-scene').systems[systemName];

### Enregistrer un système

Un système est enregistré de façon similaire à un composant. Si le nom du système correspond au nom du composant, on peut y accéder directement depuis le composant via `this.system`.

    AFRAME.registerSystem('nom-systeme', {
        schema: {},
        init: function () {},
        ...
    });

## Interactions et événements

Quand on manipule les différentes entités, il est préférable de le faire dans un composant. Comme A-Frame est basé sur HTML, on peut accéder aux entités en utilisant des méthode de sélection comme `.querySelector()` par exemple.

> [!TIP]
> À l'intérieur d'un composant, on a un accès direct à l'entité liée via `this.el` et à la scène via `this.el.sceneEl`.

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
> Pour des raisons de performances, il est préférable de mettre à jour les valeurs de position, rotation, scale et visible via three.js directement.

    el.object3D.position.x += 5

En ce qui concerne les événements, on peut en émettre avec la fonction `.emit()` et en écouter avec `addEventListener()`.

    el.emit('physicscollided', {collidingEntity: anotherEl}, false);

    el.addEventListener('physicscollided', event => {
        console.log('Entity collided with', event.detail.collidingEntity);
    });

## Scène basique

## Modèles 3D

## Gestion d'assets

## Animation

