# Exercices pratiques de RA avec A-Frame

La RA s’appuie essentiellement sur les mêmes concepts techniques que ceux qui ont été introduit lors du cours précédent sur la VR, à savoir le suivi de position (3/6DoF) d’un corps rigide (un périphérique tel qu’une tablette ou des lunettes), lui-même guidé par les mouvements d’un·e utilisateur·ice. Les mêmes typologies de tracking peuvent exister (outside-in ; inside-out), bien que par nature le mode inside-out est bien plus répandu (c’est-à-dire que le suivi de position est réalisé grâce à des données intégrées au périphérique). La différence fondamentale est l’ajout d’une texture «camera» en fond, avec l’ambition que les objets virtuels s’y ancrent de façon réaliste. 

Pour faire de la RA, il faut : 
•	Intégrer un background avec une texture caméra, pour créer la couche «réalité»
•	*n* objets virtuels qui s’ancrent, c-à-d qui interagissent avec cette couche de «réalité» 
•	Un système de suivi positionnel pour compenser et déterminer la position des objets virtuels, afin qu’ils semblent s’ancrer dans la réalité. 

Plusieurs solutions existent, y compris pour le web. Nous allons utiliser ici une approche basée sur l’utilisation du framework A-frame.js, initialement conçu pour la VR, mais qui permet également de faire de la RA. Le framework est basé sur une architecture hiérarchique de type «Entity Component System», normatif dans le monde du jeu vidéo. A-Frame permet de gérer des scènes 3D et les fonctions du mode RA du standard WebXR, qui donne accès aux données de la station inertielle du périphérique ainsi qu’à ses caméras et à la fusion de ces données pour faire du suivi positionnel. 

Dans les scènes A-frame, des objets sont positionnés selon un système de coordonnées locales, sur une unité métrique (1 = 1 m). La position initiale de la caméra (virtuelle) est l’origine. À mesure que la caméra bouge, elle est trackée et la position de la scène 3D environnante est compensée de manière inversée par rapport au suivi de la caméra: i.e. si le tracking détecte que la caméra s’est déplacée d’1 mètre en avant, A-frame «déplacera» la scène 3D d’1 mètre en arrière. 

## Marche à suivre:  
### 1. Créez un compte [glitch](https://glitch.com/) si vous souhaitez sauvegarder vos modifications. 
### 2. Sur votre laptop, allez sur [glitch](https://glitch.com/edit/#!/sic1-ar) ([sur un navigateur supportant webXR](https://caniuse.com/webxr))
### 3. Cliquez sur «remix» pour créer votre propre copie et éditer le projet. Un nom aléatoire lui sera attribué (vous pouvez le modifier sous «settings»). 
### 4. Sur la tablette, allez sur [https://***-nom-de-votre-projet.glitch.me](https://glitch.com/edit/#!/***-nom-de-votre-projet) pour voir le résultat (vous pouvez également visualisez le résultat sur votre laptop, mais la navigation est limité au clavier WASD + souris). 
### 5. Entrez en mode «RA» au moyen du bouton en bas à droite. Rafraichissez la page pour voir vos modifications sur le projet. 

[projet RA de base](https://glitch.com/edit/#!/sic1-ar)

Le premier exercice (“index.html”) 

Dans notre cas, nous souhaitons faire de la RA géolocalisée : Les objets virtuels dont nous disposons ont des attributs de pose exprimés en coordonnées géographiques. Afin de pouvoir placer ces éléments par rapport à l’interface utilisateur du périphérique, il faut :
1.	Récupérer la position géographique du périphérique mobile 
2.	Récupérer l’orientation (absolue) de la caméra du périphérique
3.	Récupérer les autres mesures de pose 6DoF de la caméra (pitch, roll)
4.	Convertir les coordonnées géographiques des objets virtuels en coordonnées locales, par rapport à l’origine (la caméra virtuelle), d’après ses données de pose 

<>

Pour cela, nous allons utiliser une librairie que nous avons développée au MEI pour intégrer de la géoinformation dans A-Frame : LBAR.js (pour Location-based Augmented Reality) <https://github.com/MediaComem/LBAR.js/>
