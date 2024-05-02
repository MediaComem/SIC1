# Exercices pratiques de RA avec des géodonnées avec A-Frame

## Table des matières <!-- omit in toc -->

- [Introduction](#introduction)
- [Importer une tile 2D avec Mapzen Tangram](#exemple-1)
- [Ajouter la position GPS du périphérique mobile sur la carte](#exemple-2)
- [Importer des fichiers `.geojson` dans a-frame](#exemple-3)
- [Importer des tiles 3D avec `Cesium.js`](#exemple-4)

  
## <a name="introduction">Introduction</a> 
L’utilisation de géodonnées en RA peut se faire de différentes façons, avec toutefois beaucoup moins de souplesse et d’options que dans le cas de web "non-spatial". Dans a-frame, chaque objet présent dans la scène devient un objet 3D avec ce que cela comprend de coût computationnel à calculer. Il est possible d’importer des cartes, mais pour pouvoir être restituée dans un espace 3D, la tuile invoquée deviendra une image statique qui est attribuée en tant que texture à une primitive de type "plane". Il est également possible d’importer des tuiles 3D via CesiumIon, sans avoir toutes les options que ce framework offre traditionnellement. Finalement nous verrons un exemple de RA géolocalisée dont le but est de faire apparaître des objets virtuels auxquels sont attribués des coordonnées géographiques. Pour tester, il faudra donc sortir pour avoir accès aux données GNSS et aller à l’endroit paramétré. Pour cela, nous allons utiliser une librairie que nous avons développée au MEI pour intégrer de la géoinformation et convertir des positions globales en positions locales dans A-Frame: [LBAR.js](<https://github.com/MediaComem/LBAR.js/>) (Location-Based Augmented Reality). 

## <a name="exemple-1">Importer une tile 2D avec Mapzen Tangram</a> 

1. Ouvrez la page `index.html`. Importer le [component "Mapzen Tangram"](https://github.com/mattrei/aframe-tangram-component?tab=readme-ov-file) dans `head`:
   `<script src="https://unpkg.com/aframe-tangram-component/dist/aframe-tangram-component.min.js"></script>`
2. Importez les assets suivants à l’intérieur d’un objet `<a-assets></a-assets>`, au début de votre `<a-scene>`. Il s’agit de deux styles de carte
   ```
        <a-asset-item id="zip-style" src="https://www.nextzen.org/carto/bubble-wrap-style/9/bubble-wrap-style.zip" />
        <a-asset-item id="blue-style" src="https://www.nextzen.org/carto/refill-style/11/refill-style.zip" />
   ```
3. Créez un objet `<a-tangram-map></a-tangram-map>` avec les attributs suivants:
   ```
    api-key="UJcQwnnfQnWcPtKe2aYaEw"
    position="0 0 -2"
    width="4"
    height="3"
    map-style="#blue-style"
    center="6.61036944949248, 46.523261642620966"
    zoom="16"
    px-world-ratio="100"

   ```
4. Ouvrez la page sur la tablette pour tester en mode RA. 
5. Remplacez le style de la carte (`map-style=…`) par `#zip-style`. Modifiez les coordonnées (`center=…`), le niveau de zoom, la taille de la carte, etc. 

## <a name="exemple-2">Ajouter la position GPS du périphérique mobile sur la carte (´map-gps.html´)</a> 
1. Sur la page ´map-gps.html´, downgradez* la version d’a-frame vers la version 0.8.2:
`<script src="https://aframe.io/releases/0.8.2/aframe.min.js"></script>`
* Notez que si vous utilisez cette dépendance dans votre projet, les fonctionnalités apparues dans les version ultérieures d’a-frame pourraient ne pas être disponibles. 
3. Dans la `<a-scene>`, entre les balises `<a-assets></a-assets>`, importez l’asset (le style cartographique) "tron-style":
`<a-asset-item id="tron-style" src="https://www.nextzen.org/carto/tron-style/6/tron-style.zip" />`
4. Dans la scene, sous les assets, ajoutez l’`<a-entity>` qui suit:
   ```
   <a-entity 
        position="0 0 -2"
        geometry="primitive: plane; width: 7; height: 5;"
        tangram-map="apiKey: UJcQwnnfQnWcPtKe2aYaEw; style: #tron-style;
        ">
      </a-entity>
   ```
5. à la fin de la page, entre la balise fermante `</body>` et `</html>`, insérer le javascript qui suit:
   ```
    <script>
      var mapEl = document.querySelector('[tangram-map]');
      var markerEl = document.querySelector('#marker');
      var setProperty = window.AFRAME.utils.entity.setComponentProperty;
      mapEl.addEventListener('tangram-map-loaded', function() {
        navigator.geolocation.watchPosition(function(position) {
          var long = position.coords.longitude;
          var lat = position.coords.latitude;
          setProperty(mapEl, 'tangram-map.center', long + ', ' + lat);
          setProperty(mapEl, 'tangram-map.zoom', '16');
          setProperty(markerEl, 'position', mapEl.components["tangram-map"].project(long, lat));
          setProperty(markerEl, 'visible', true);
        }, function(error) {
          console.error(error);
        });
      });
    </script>
   ```
Notez la méthode `watchPosition()` qui permet d’obtenir (moyennant l’autorisation de l’utilisateur dans le navigateur) les coordonnées géographiques du périphérique. 
5. En tant qu’enfant de l’entity plane qui contient le component `tangram-map`, ajouter une primitive de type sphère avec l’`id="marker"`:
`<a-sphere id="marker" color="#EF2D5E" position="0 0 0" visible="false" radius="0.1"></a-sphere>`

6. Tester dans le navigateur de la tablette, si possible en extérieur pour obtenir une géolocation plus précise. 

## <a name="exemple-3">Importer des fichiers `.geojson` dans a-frame</a> 
1. Ouvrez la page `geojson.html`. Importez les librairies [a-frame geojson component](https://github.com/mattrei/aframe-geojson-component) et `d3.js` dans la `<head>`: 
   ```
   <script src="https://unpkg.com/aframe-geojson-component/dist/aframe-geojson-component.min.js"></script>
   <script src="https://d3js.org/d3.v5.js"></script>
   ```
2. Ajoutez les attributs suivants dans la balise ouvrante `<a-scene>`:
`camera-transform-controls cursor="rayOrigin: mouse"`
cela permet en cliquant la rotation de la scene autour d’un axe centré sur un objet.
3. Importez au début de la scène, dans des balises `<a-assets>…</a-assets>`, les deux fichiers geojson qui suivent:
   - `<a-asset-item id="world-geojson" src="assets/world-50m.v1.json" />`pour les frontières nationales
   - `<a-asset-item id="lakes" src="https://cdn.glitch.global/77697557-e2e3-48f6-b7c1-14eb74f520c0/ne_50m_lakes.geojson?v=1713815282536" />` pour les lacs
4. Dans l’entity qui se trouve dans la scène, ajoutez l’entity primitive de type sphère (en tant qu’enfant) suivante:
`       <a-entity 
        geometry="primitive: sphere; radius: 4;"
        material="color: #000;"
        geojson="src: #world-geojson; topologyObject: countries;"
        />    
`
le component geojson permet d’appliquer le fichier `.geojson` souhaité en tant que texture de la sphère.
5. Sous cette entity, ajoutez une deuxième entity avec les lacs:
   ```
        <a-entity 
        geometry="primitive: sphere; radius: 4;"
        material="color: #0096C7;"
        geojson="src: #lakes; topologyObject: lakes;"
        />
   ```
6. Sur l’entity parente, ajoutez l’attribut suivant pour animer la sphère et la faire tourner (1 tour complet en 10 secondes):
`animation="property: rotation; to: 360 360 360; loop: true; dur: 100000"`


## <a name="exemple-4">Importer des tiles 3D avec `Cesium.js`</a> 
1. 

## <a name="exemple-5">Exemple 5</a> 



Dans notre cas, nous souhaitons faire de la RA géolocalisée : Les objets virtuels dont nous disposons ont des attributs de pose exprimés en coordonnées géographiques. Afin de pouvoir placer ces éléments par rapport à l’interface utilisateur du périphérique, il faut :
1.	Récupérer la position géographique du périphérique mobile 
2.	Récupérer l’orientation (absolue) de la caméra du périphérique
3.	Récupérer les autres mesures de pose 6DoF de la caméra (pitch, roll)
4.	Convertir les coordonnées géographiques des objets virtuels en coordonnées locales, par rapport à l’origine (la caméra virtuelle), d’après ses données de pose 

<>
