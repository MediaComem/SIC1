# Exercices pratiques de RA avec A-Frame

La RA s’appuie essentiellement sur les mêmes concepts que ceux qui ont été introduit lors du cours précédent sur la VR, à savoir le suivi de position (3/6DoF) d’un corps rigide (un périphérique tel qu’une tablette ou des lunettes), lui-même guidé par les mouvements d’un·e utilisateur·ice. Les mêmes typologies de tracking peuvent exister (outside-in ; inside-out), bien que par nature le mode inside-out est bien plus répandu (c’est-à-dire que le suivi de position est réalisé grâce à des données intégrées au périphérique). 

Plusieurs solutions existent, y compris pour le web. Je vais décrire ici une approche qui repose sur l’utilisation du framework A-frame.js, initialement conçu pour la VR, mais qui permet également de faire de la RA. Le framework est basé sur une architecture hiérarchique de type «Entity Component System», normatif dans le monde du jeu vidéo. A-Frame permet de gérer des scènes 3D et WebXR. Dans ces scènes, des objets sont positionnés selon un système de coordonnées locales, dont la position initiale de la caméra (virtuelle) est l’origine. Pour faire de la RA, il faut : 
•	Intégrer un background avec une texture caméra, pour créer la couche «réalité»
•	Déterminer la position des objets virtuels afin qu’ils interagissent avec la réalité selon la logique souhaitée

[RA avec A-frame](https://basic-ar-a-frame.glitch.me)
[code](https://glitch.com/edit/#!/basic-ar-a-frame)

Dans notre cas, nous souhaitons faire de la RA géolocalisée : Les objets virtuels dont nous disposons ont des attributs de pose exprimés en coordonnées géographiques. Afin de pouvoir placer ces éléments par rapport à l’interface utilisateur du périphérique, il faut :
1.	Récupérer la position géographique du périphérique mobile 
2.	Récupérer l’orientation (absolue) de la caméra du périphérique
3.	Récupérer les autres mesures de pose 6DoF de la caméra (pitch, roll)
4.	Convertir les coordonnées géographiques des objets virtuels en coordonnées locales, par rapport à l’origine (la caméra virtuelle), d’après ses données de pose 

<>

Pour cela, nous allons utiliser une librairie que nous avons développée au MEI pour intégrer de la géoinformation dans A-Frame : LBAR.js (pour Location-based Augmented Reality) <https://github.com/MediaComem/LBAR.js/>
