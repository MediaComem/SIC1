# Exercices pratiques de RA avec des géodonnées avec A-Frame

## Table des matières <!-- omit in toc -->

- [1. Introduction](#introduction)
- [2. Installation](#installation)
- [3. Importer une tile 2D avec Mapzen Tangram](#exemple-1)
- [4. Ajouter la position GPS du périphérique mobile sur la carte](#exemple-2)
- [5. Importer des fichiers `.geojson` dans a-frame](#exemple-3)
- [6. Importer des tiles 3D avec `Cesium.js`](#exemple-4)
- [7. Importer des tiles 3D avec `Cesium.js` II](#exemple-5)
- [8. Pour aller plus loin…](#exemple-6)
  
## <a name="introduction">1. Introduction</a> 
L’utilisation de géodonnées en RA peut se faire de différentes façons, avec toutefois beaucoup moins de souplesse et d’options que dans le cas de web "non-spatial". Dans a-frame, chaque objet présent dans la scène devient un objet 3D avec ce que cela comprend de coût computationnel à calculer. Il est possible d’importer des cartes, mais pour pouvoir être restituée dans un espace 3D, la tuile invoquée deviendra une image statique qui est attribuée en tant que texture à une primitive de type "plane". Il est également possible d’importer des tuiles 3D via CesiumIon, sans avoir toutes les options que ce framework offre traditionnellement. Finalement nous verrons un exemple de RA géolocalisée dont le but est de faire apparaître des objets virtuels auxquels sont attribués des coordonnées géographiques. Pour tester, il faudra donc sortir pour avoir accès aux données GNSS et aller à l’endroit paramétré. Pour cela, nous allons utiliser une librairie que nous avons développée au MEI pour intégrer de la géoinformation et convertir des positions globales en positions locales dans A-Frame: [LBAR.js](<https://github.com/MediaComem/LBAR.js/>) (Location-Based Augmented Reality). 

## <a name="Installation">2. Installation</a>
- Pour commencer, rendez-vous sur l’url [https://glitch.com/edit/#!/sic1-ar-geo](https://glitch.com/edit/#!/sic1-ar-geo) et créez un clone ("remix") du projet. 

## <a name="exemple-1">3. Importer une tile 2D avec Mapzen Tangram</a> 

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

## <a name="exemple-2">4. Ajouter la position GPS du périphérique mobile sur la carte (´map-gps.html´)</a> 
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

## <a name="exemple-3">5. Importer des fichiers `.geojson` dans a-frame</a> 
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


## <a name="exemple-4">6. Importer des tiles 3D avec `Cesium.js`</a> 
1. Ouvrez la page `globe.html`. Downgradez la version d’a-frame à la version 0.7.1.
2. Importez dans la `head` la librairie [`<a-terrain>`](https://github.com/anselm/aterrain), [aframe-orbit-controls-component.js](https://github.com/tizzle/aframe-orbit-controls-component), Cesium.js, et [aframe-environment-component](https://www.npmjs.com/package/aframe-environment-component):
   ```
   <script src="js/aframe-orbit-controls-component.js"></script>
    <script src="js/Cesium.js"></script>
    <script src="js/aframe-aterrain-component.js"></script>
    <script src="https://unpkg.com/aframe-environment-component@1.0.0/dist/aframe-environment-component.min.js"></script>
   ```
3. Dans la scène, créez une entity avec l’`id="world"` et le component `a-terrain`:
`<a-entity id="world" position="0 0 0" visible="true" a-terrain="radius:1000; observer:camera">`
4. Créez une entity "enfant" de cette entity avec le component `a-location` et les coordonnées géographiques de votre choix:
   `<a-entity a-location="lat:46.523261642620966; lon:6.61036944949248; radius:1010;"></a-entity>`
5. Créez une entity "enfant" de cette entity avec l’attribut suivant (pour se positionner à la perpendiculaire de son parent):
   `<a-entity rotation="-90 0 0"></a-entity>`
6. Créez une entity "enfant" de type `gltf-model`de cette entity avec une `scale` importante et une `src=…` qui pointe vers un assets de type `gltf`:
 `<a-gltf-model scale="100 100 100" src="https://cdn.glitch.global/77697557-e2e3-48f6-b7c1-14eb74f520c0/drapeau-jaune.glb?v=1713802540021"><a-gltf-model>`
7. Bonus: créez une entity de type `<a-animation>` comme "enfant" de l’entity `<a-gltf-model>` pour faire tourner le marqueur sur lui-même:
   `<a-animation attribute="rotation" dur="10000" from="0 0 0" to="0 360 0" easing="linear" repeat="indefinite"></a-animation>`
8. Testez différentes valeurs pour les attributs de l’entity caméra pour trouvez une interaction confortable (scroll/zoom, etc.).  

## <a name="exemple-5">7. Importer des tiles 3D avec `Cesium.js` II</a> 
1. Ouvrez la page `cesium-tile.html`. Downgradez la version d’a-frame à la version 0.7.1.
2. Dans la `<head>`, importez les librairies suivantes:
   ```
   <script src="js/Cesium.js"></script>
   <script src="js/aframe-aterrain-component.js"></script>
   <script src="https://fernandojsg.github.io/aframe-camera-transform-controls-component/dist/aframe-camera-transform-controls-component.min.js"> </script>

   ```
 3. Dans la scène, créez une entity avec le component `<a-terrain>`. Donnez-lui les attributs suivants: 
  ```
  <a-entity
  a-terrain="
    fovpad:4;
  	latitude:46.523261642620966;
  	longitude:6.61036944949248;
  	stretch:2;
  	lod:13;
  	elevation:200;
  	radius:10000;
	">
	</a-entity>
  ```
4. Modifiez les coordonnées, le lod (level of detail), etc. Attention: en fonction de l’endroit que vous sollicitez, les levels of details élevé ne sont peut-être pas disponible. Attention également à avoir une élevation supérieure à la zone sollicitée, sans quoi la tile cesium n’est pas visible.
5. Dans l’inspecteur d’a-frame (ctrl+alt+i), repositionnez la camera avec les handles de position (flèches verte, rouge, bleue) et reportez les valeurs dans votre entity camera.
   
## <a name="exemple-6">8. Pour aller plus loin…</a> 
1. Il existe plusieurs autres librairies pour traiter/importer/gérer des géodonnées dans a-frame, comme par exemple:
   - [vrmap](https://github.com/KaiRo-at/vrmap)
   - [aframe-map (mapbox)](https://github.com/jesstelford/aframe-map)
   - [aframe-openlayers](https://github.com/lcalisto/aframe-openlayers-component)
2. Ou, hors a-frame:
   - [ESRI/arcgis](https://medium.com/@seremetonurhan/display-real-time-weather-information-on-your-web-scene-maps-with-arcgis-javascript-sdk-32cc796c667c)

---
[Chapitre suivant : LBAR.js](LBAR-js.md)
---
