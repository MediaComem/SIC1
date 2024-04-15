# Introduction à la RA

## Table des matières <!-- omit in toc -->

- [Introduction à la RA](#introduction-à-la-ra)
  - [Principe de la RA](#principe-ra)
  - [Typologie](#typologie-ra)
    - [Par degré de «virtualité»](#virtualité)
    - [Par périphérique](#périphérique)
    - [Par méthode de tracking](#méthode-tracking)
		1. [Marker-based AR](#marker-based-ar)
		2. [Markerless AR](#markerless-ar)
		3. [Location-based AR](#location-based-ar)
		4. [Superimposition-based AR](#superimposition-based-ar)
		5. [Projection-based AR](#projection-based-ar)
		
  - [Problématiques d’utilisabilité VR vs RA](#problematiques)
    - [A](#A)
    - [B](#B)
  - [Résumé](#résumé)
  - [Références](#références)

## <a name="principe-ra">Principes de la RA</a>

Conceptuellement, la RA se distingue de la VR par la proportion d’éléments virtuels qu’elle comprend par rapport aux éléments réels: en VR, l’entier du champ de vision est recouvert par des éléments visuels (ciel, background, objets…): ils **occluent** la partie visible de la réalité, même si les éléments réels (le sol, les murs…) restent perceptibles sur par les autres modalités sensorielles que la vision (toucher, odorat). L’ouïe peut ou non être occluse selon qu’on utilise des écouteurs en plus du casque.

En RA, on va conserver une grande partie d’éléments réels: sur une tablette, on aura généralement une vue caméra en fond; sur des lunettes, on verra la réalité à travers un écran transparent. L’exercice consiste à **augmenter** cette réalité perçue en y introduisant des éléments virtuels. Cela offre des possibilités intéressantes en interaction humain-machine (IHM), puisque cela permet d’imaginer des systèmes où toutes les connaissances relatives à un objet donné serait visibles dans un même espace informationnel. 

Pour exploiter au mieux les affordances de la RA, on va essayer de donner à ces éléments une cohérence avec le monde réel qui les entoure, ce qui introduit des difficultés techniques de mise en œuvre, puisqu’il faut alors connaître et mesurer avec précision ce monde réel. 


## <a name="typologie-ra">Typologie</a>
### <a name="virtualité">Par degré de «virtualité»</a>
Il existe une grande variété de modalités pour faire de la RA ou de la VR, qui s’inscrivent sur le «continuum de la virtualité» proposé par Milgram et Kishino en 1994 [^1].
Il débute dans l’environnement réel et finirait sur un (théorique) environnement 100% virtuel (à la Matrix) ou l’ensemble de nos perceptions serait simulé de telle sorte qu’il serait impossible de percevoir la différence avec la réalité.  

| ![Le continuum «Réel-Virtuel» (adapté de (Milgram et al., 1994))](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/Virtuality_continuum_2-en.svg/2880px-Virtuality_continuum_2-en.svg.png) | 
|:--:| 
| *Le continuum «Réel-Virtuel» (adapté de (Milgram et al., 1994))* |

### <a name="périphérique">Par périphérique</a>
Au sein des interfaces qualifiées de RA, il existe une variété de types, qui peuvent être catégorisés selon les périphériques utilisés : lunettes, casque (“head-mounted-display”); smartphone, tablette (“hand-held”); tabletop AR (“projection-based”), head-up display (pare-brise de véhicule)… Ces catégories sont dynamiques et évoluent rapidement au gré des innovations. ![image](https://github.com/MediaComem/SIC1/assets/16796103/ac11eb15-2f92-4b87-97be-46d6b24867ae)

### <a name="méthode-tracking">Par méthode de tracking</a>
Il existe également une catégorisation selon les méthodes utilisées pour faire le suivi de position. 

#### <a name="marker-based-ar">**1. Marker-based AR**</a> Marker-based AR: lorsqu’on a recours à des algorithmes de vision par ordinateur pour faire de la reconnaissance de motif, ce qui permet de transformer des pixels en coordonnées 3D locales. L’exemple le plus connu est celui des QR codes, mais d’autres motifs (y compris invisibles, ou basés sur des modèles 3D) sont possibles. Il est possible avec cette technique d’ancrer des points d’intérêt à un objet appartenant au «monde réel», en plaçant une balise à l’endroit voulu. L’avantage de cette approche est sa précision (~<1mm). Son désavantage principal est la nécessité d’avoir un relai physique dans le monde réel, qui doit également être connu en amont de l’expérience. 

#### <a name="markerless-ar">Markerless AR</a>
Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet 

#### <a name="location-based-ar">Location-based AR</a>
Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet 

#### <a name="superimposition-based-ar">Superimposition-based AR</a>
Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet 

#### <a name="projection-based-ar">Projection-based AR</a>
Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet Lorem Ipsum dolor simet 


---

[Chapitre suivant : Base de A-Frame](Base-A-Frame.md)
---
## <a name="references">références:</a>
[^1]: Milgram, P.; Kishino, F. A taxonomy of mixed reality visual displays. IEICE Trans. Inf. Syst. 1994,77, 1321–1329.
