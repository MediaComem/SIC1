# Exercices pratiques de RA avec des géodonnées avec A-Frame

## Table des matières <!-- omit in toc -->

- [Introduction](#introduction)
- [Exemple 1](#exemple-1)

- 
## <a name="introduction">Introduction</a> 
L’utilisation de géodonnées en RA peut se faire de différentes façons, avec toutefois beaucoup moins de souplesse et d’options que dans le cas de web "non-spatial". Dans a-frame, chaque objet présent dans la scène devient un objet 3D avec ce que cela comprend de coût computationnel à calculer. Il est possible d’importer des cartes, mais pour pouvoir être restituée dans un espace 3D, la tuile invoquée deviendra une image statique qui est attribuée en tant que texture à une primitive de type "plane". Il est également possible d’importer des tuiles 3D via CesiumIon, sans avoir toutes les options que ce framework offre traditionnellement. Finalement nous verrons un exemple de RA géolocalisée dont le but est de faire apparaître des objets virtuels auxquels sont attribués des coordonnées géographiques. Pour tester, il faudra donc sortir pour avoir accès aux données GNSS et aller à l’endroit paramétré. Pour cela, nous allons utiliser une librairie que nous avons développée au MEI pour intégrer de la géoinformation et convertir des positions globales en positions locales dans A-Frame: [LBAR.js](<https://github.com/MediaComem/LBAR.js/>) (Location-Based Augmented Reality). 

## <a name="exemple-1">Exemple 1</a> 
## <a name="exemple-2">Exemple 2</a> 
## <a name="exemple-3">Exemple 3</a> 
## <a name="exemple-4">Exemple 4</a> 
## <a name="exemple-5">Exemple 5</a> 



Dans notre cas, nous souhaitons faire de la RA géolocalisée : Les objets virtuels dont nous disposons ont des attributs de pose exprimés en coordonnées géographiques. Afin de pouvoir placer ces éléments par rapport à l’interface utilisateur du périphérique, il faut :
1.	Récupérer la position géographique du périphérique mobile 
2.	Récupérer l’orientation (absolue) de la caméra du périphérique
3.	Récupérer les autres mesures de pose 6DoF de la caméra (pitch, roll)
4.	Convertir les coordonnées géographiques des objets virtuels en coordonnées locales, par rapport à l’origine (la caméra virtuelle), d’après ses données de pose 

<>
