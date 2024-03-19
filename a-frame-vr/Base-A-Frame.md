# Bases de A-Frame

A-Frame est un framework web open-source dédié au développement d'expériences en réalité virtuelle et augmentée. Basé sur HTML et Three.js, il permet facilement de créer du contenu en VR.

## Architecture

A-Frame adopte une architecture Entity-Component-System. Ce modèle se compose des 3 éléments suivants :

- **Entité** : conteneur permettant d'accueillir des composants. Sans composant, les entités ne sont pas rendues, à l'image de `<div>` vides.

- **Composant** : module réutilisable attachable à des entités, pour leur donner une apparence, un comportement ou une fonctionnalité.
- **Système** : module qui permet de gérer des données globales, des services et des classes pour les composants.

Plus concrétement, ces éléments sont intégrer comme suit dans A-Frame.

## Entité

La syntaxe de base d'une entité se rapproche d'une balise HTML :

    <a-entity></a-entity>

Il existe cependant des éléments de base préconstruits, appelés _primitives_, qui permettent de construire facilement une scène sans devoir plonger dans l'architecture sous-jacente. On retrouve notamment des éléments tels que `a-box`, `a-sphere` ou même `a-sky`.

Consulter la doc pour créer des [primitives personnalisées](https://aframe.io/docs/1.5.0/introduction/html-and-primitives.html#registering-a-primitive).

## Composant

Les composants sont attachés aux entités comme des attributs sur des éléments HTML.

    <a-entity position="0 0 1"></a-entity>


## Gestion d'assets

