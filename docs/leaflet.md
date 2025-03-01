# Leaflet

https://leafletjs.com/

## Introducción y Configuración

https://leafletjs.com/examples/quick-start/

Instalación y configuración de Leaflet (CDN, NPM, en un proyecto HTML).

Creación de un contenedor para el mapa (`div` con `id`).

Inicialización de un mapa (`L.map()`). https://leafletjs.com/reference.html#map-example

Agregar una capa base con `L.tileLayer()`. https://leafletjs.com/reference.html#tilelayer

Configuración de vista y zoom.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Mapa Básico con Leaflet</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
    <style>
      #map {
        height: 500px;
      }
    </style>
  </head>
  <body>
    <div id="map"></div>
    <script>
      const map = L.map("map", {
        center: [40.7128, -74.006],
        zoom: 13
      });
      L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution: "© OpenStreetMap contributors",
      }).addTo(map);
    </script>
  </body>
</html>
```

### Mapa a pantalla completa

https://leafletjs.com/examples/mobile/

```html hl_lines="10-17"
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Mapa Básico con Leaflet</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
    <style>
      body {
        padding: 0;
        margin: 0;
      }
      html, body, #map {
        height: 100%;
        width: 100vw;
      }
    </style>
  </head>
  <body>
    <div id="map"></div>
    <script>
      const map = L.map("map", {
        center: [40.7128, -74.006],
        zoom: 13
      });
      L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution: "© OpenStreetMap contributors",
      }).addTo(map);
    </script>
  </body>
</html>
```

### Mantener la vista del mapa - Hash

Plugins: https://leafletjs.com/plugins.html#bookmarked-panzoom

https://github.com/mlevans/leaflet-hash


```html hl_lines="9 10 30"
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Mapa Básico con Leaflet</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-hash/{{ versions.leaflet_hash }}/leaflet-hash.min.js"
      crossorigin="anonymous"></script>
    <style>
      body {
        padding: 0;
        margin: 0;
      }
      html, body, #map {
        height: 100%;
        width: 100vw;
      }
    </style>
  </head>
  <body>
    <div id="map"></div>
    <script>
      const map = L.map("map", {
        center: [40.7128, -74.006],
        zoom: 13
      });

      const hash = new L.Hash(map);

      const osm = L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution: "© OpenStreetMap contributors",
      }).addTo(map);
    </script>
  </body>
</html>
```

## Agregando datos al mapa

### Markers

https://leafletjs.com/reference.html#marker

https://leafletjs.com/examples/custom-icons/

```js linenums="36" hl_lines="1-11"
const greenIcon = L.icon({
  iconUrl: 'leaf-green.png',
  shadowUrl: 'leaf-shadow.png',
  iconSize:     [38, 95], // size of the icon
  shadowSize:   [50, 64], // size of the shadow
  iconAnchor:   [22, 94], // point of the icon which will correspond to marker's location
  shadowAnchor: [4, 62],  // the same for the shadow
  popupAnchor:  [-3, -76] // point from which the popup should open relative to the iconAnchor
});

const marker = L.marker([40.7048, -74.0490], {icon: greenIcon}).addTo(map);
```

### WMS

https://leafletjs.com/reference.html#tilelayer-wms

https://leafletjs.com/examples/wms/wms.html

```js linenums="48" hl_lines="1-3"
const wmsLayer = L.tileLayer.wms('http://ows.mundialis.de/services/service?', {
  layers: 'SRTM30-Colored-Hillshade',
}).addTo(map);
```

### GeoJSON

https://leafletjs.com/reference.html#geojson

https://leafletjs.com/examples/geojson/

```js linenums="56" hl_lines="1-14"
const geojsonFeature = {
  "type": "Feature",
  "properties": {
      "name": "Central Park Zoo",
      "amenity": "zoo",
      "popupContent": "El Zoo de la peli Magadascar!"
  },
  "geometry": {
      "type": "Point",
      "coordinates": [-73.97185, 40.76761]
  }
};

L.geoJSON(geojsonFeature).addTo(map);
```

#### GeoJSON externo - Leaflet Ajax

https://github.com/calvinmetcalf/leaflet-ajax

```html linenums="11" hl_lines="1-2"
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-ajax/{{ versions.leaflet_ajax }}/leaflet.ajax.min.js" integrity="sha512-Abr21JO2YqcJ03XGZRPuZSWKBhJpUAR6+2wH5zBeO4wAw4oksr8PRdF+BKIRsxvCdq+Mv4670rZ+dLnIyabbGw==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
```

```js linenums="73" hl_lines="1"
const estadios = new L.GeoJSON.AJAX("https://raw.githubusercontent.com/cageyjames/GeoJSON-Ballparks/refs/heads/master/ballparks.geojson").addTo(map);
```

#### `pointToLayer`

https://leafletjs.com/reference.html#geojson-pointtolayer

```js linenums="71" hl_lines="1-8 11-13"
const geojsonMarkerOptions = {
  radius: 8,
  fillColor: "#ff7800",
  color: "#000",
  weight: 1,
  opacity: 1,
  fillOpacity: 0.8
};

L.geoJSON(geojsonFeature, {
  pointToLayer: function (feature, latlng) {
    return L.circleMarker(latlng, geojsonMarkerOptions);
  }
}).addTo(map);
```

```js linenums="86" hl_lines="1-6 9-11"
const baseballIcon = L.icon({
  iconUrl: 'https://betaserver.icgc.cat/mapicons/baseball.png',
  iconSize:     [37, 37], // size of the icon
  iconAnchor:   [13, 37], // point of the icon which will correspond to marker's location
  popupAnchor:  [-13, -37] // point from which the popup should open relative to the iconAnchor
});

const estadios = new L.GeoJSON.AJAX("https://raw.githubusercontent.com/cageyjames/GeoJSON-Ballparks/refs/heads/master/ballparks.geojson", {
  pointToLayer: function (feature, latlng) {
    return L.marker(latlng, {icon: baseballIcon});
  }
}).addTo(map);
```

#### `onEachFeature`

https://leafletjs.com/reference.html#geojson-oneachfeature

```js linenums="99" hl_lines="1-7"
new L.GeoJSON.AJAX(" https://raw.githubusercontent.com/PublicaMundi/MappingAPI/refs/heads/master/data/geojson/us-states.json", {
  onEachFeature: function (feature, layer) {
    layer.bindPopup(function (layer) {
      return layer.feature.properties.name;
    });
  }
}).addTo(map);
```

#### Filtro

https://leafletjs.com/reference.html#geojson-filter

```js linenums="99" hl_lines="6-9"
const estados = new L.GeoJSON.AJAX(" https://raw.githubusercontent.com/PublicaMundi/MappingAPI/refs/heads/master/data/geojson/us-states.json", {
  onEachFeature: function (feature, layer) {
    layer.bindPopup(function (layer) {
      return layer.feature.properties.name;
    });
  },
  filter: function (geoJsonFeature) {
    return geoJsonFeature.properties.density > 60;
  }
}).addTo(map);
```

### Map panes

https://leafletjs.com/reference.html#map-pane


## Control de capas

### Layer control

https://leafletjs.com/reference.html#control-layers

https://leafletjs.com/examples/layers-control/

```js linenums="108" hl_lines="1-13"
var baseLayers = {
    "Hillshade": wmsLayer,
    "OpenStreetMap": osm
};

var overlays = {
    "Marker": marker,
    "Baseball": estadios,
    "Estados": estados
};

L.control.layers(baseLayers, overlays).addTo(map);
```

Plugins: https://leafletjs.com/plugins.html#layer-switching-controls

## Dar estilos a los datos

### Estilo

https://leafletjs.com/reference.html#geojson-style

https://leafletjs.com/reference.html#path

```js linenums="99" hl_lines="9-13"
const estados = new L.GeoJSON.AJAX(" https://raw.githubusercontent.com/PublicaMundi/MappingAPI/refs/heads/master/data/geojson/us-states.json", {
  onEachFeature: function (feature, layer) {
    layer.bindPopup(function (layer) {
      return layer.feature.properties.name;
    });
  },
  filter: function (geoJsonFeature) {
    return geoJsonFeature.properties.density > 60;
  },
  style: {
    fillColor: "#b471e3",
    color: "#7321de"
  }
}).addTo(map);
```

### Tematicos

https://leafletjs.com/examples/choropleth/

```js linenums="98" hl_lines="1-21 32"
function getColor(d) {
  return d > 1000 ? '#800026' :
    d > 500  ? '#BD0026' :
    d > 200  ? '#E31A1C' :
    d > 100  ? '#FC4E2A' :
    d > 50   ? '#FD8D3C' :
    d > 20   ? '#FEB24C' :
    d > 10   ? '#FED976' :
                '#FFEDA0';
}

function getStyle(feature) {
  return {
    fillColor: getColor(feature.properties.density),
    weight: 2,
    opacity: 1,
    color: 'white',
    dashArray: '3',
    fillOpacity: 0.7
  };
}

const estados = new L.GeoJSON.AJAX(" https://raw.githubusercontent.com/PublicaMundi/MappingAPI/refs/heads/master/data/geojson/us-states.json", {
  onEachFeature: function (feature, layer) {
    layer.bindPopup(function (layer) {
      return layer.feature.properties.name;
    });
  },
  filter: function (geoJsonFeature) {
    return geoJsonFeature.properties.density > 60;
  },
  style: getStyle
}).addTo(map);
```

## Eventos

https://leafletjs.com/reference.html#event-objects

```js linenums="144" hl_lines="1-15"
const popup = L.popup();

function onMapClick(e) {
  popup
    .setLatLng(e.latlng) // Sets the geographical point where the popup will open.
    .setContent(
      "Has hecho click en la coordenada:<br> " +
        e.latlng.lat.toString() +
        "," +
        e.latlng.lng.toString()
    ) // Sets the HTML content of the popup.
    .openOn(map); // Adds the popup to the map and closes the previous one.
}

map.on("click", onMapClick);
```

## Crear un control

https://leafletjs.com/examples/extending/extending-3-controls.html

https://leafletjs.com/plugins.html#user-interface

### EasyButton

https://github.com/CliffCloud/Leaflet.EasyButton

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@{{ versions.bootstrap_icons }}/font/bootstrap-icons.min.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/leaflet-easybutton@{{ versions.leaflet_easybutton }}/src/easy-button.css">
<script src="https://cdn.jsdelivr.net/npm/leaflet-easybutton@{{ versions.leaflet_easybutton }}/src/easy-button.js"></script>
```

```js linenums="163" hl_lines="1-5"
L.easyButton('bi bi-emoji-smile', function(btn, map){
  var barcelona = [41.3857, 2.2076];
  map.setView(barcelona, 12);
}).addTo( map );
```

## Context menu

https://github.com/aratcliffe/Leaflet.contextmenu

## Barra de edición

https://leaflet.github.io/Leaflet.draw/docs/leaflet-draw-latest.html

```html
<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.draw/{{ versions.leaflet_draw }}/leaflet.draw.css"
  crossorigin="anonymous"
/>

<script
  src="https://cdnjs.cloudflare.com/ajax/libs/leaflet.draw/{{ versions.leaflet_draw }}/leaflet.draw.js"
  crossorigin="anonymous"
></script>
```

```js
// FeatureGroup is to store editable layers
const drawnItems = new L.FeatureGroup();
map.addLayer(drawnItems);
const drawControl = new L.Control.Draw({
  edit: {
    featureGroup: drawnItems,
  },
  position: 'topright'
});
map.addControl(drawControl);

map.on(L.Draw.Event.CREATED, function (e) {
  console.log(e);
  const type = e.layerType,
    layer = e.layer;
  if (type === "marker") {
    // Do marker specific actions
  }
  // Do whatever else you need to. (save to db; add to map etc)
  drawnItems.addLayer(layer);
  console.log(layer.toGeoJSON());
});

map.on("draw:edited", function (e) {
  const layers = e.layers;
  console.log(layers);
  layers.eachLayer(function (layer) {
    //do whatever you want; most likely save back to db
  });
});
```

### Editable

https://github.com/Leaflet/Leaflet.Editable


## Barra lateral

https://github.com/turbo87/sidebar-v2/

```html
<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/sidebar-v2@{{ versions.leaflet_sidebar_v2 }}/css/leaflet-sidebar.css"
/>
<script src="https://cdn.jsdelivr.net/npm/sidebar-v2@{{ versions.leaflet_sidebar_v2 }}/js/leaflet-sidebar.js"></script>
```

```html linenums="42" hl_lines="48"
<div id="sidebar" class="sidebar sidebar-left leaflet-touch collapsed">
  <!-- Nav tabs -->
  <div class="sidebar-tabs">
    <ul role="tablist">
      <li><a href="#home" role="tab"><i class="bi bi-list"></i></a></li>
      <li><a href="#profile" role="tab"><i class="bi bi-person-fill"></i></a></li>
      <li class="disabled"><a href="#messages" role="tab"><i class="bi bi-envelope"></i></a></li>
      <li><a href="https://github.com/Turbo87/sidebar-v2" role="tab" target="_blank"><i class="bi bi-github"></i></a></li>
    </ul>

    <ul role="tablist">
      <li><a href="#settings" role="tab"><i class="bi bi-gear"></i></a></li>
    </ul>
  </div>

  <!-- Tab panes -->
  <div class="sidebar-content">
    <div class="sidebar-pane" id="home">
      <h1 class="sidebar-header">
        sidebar-v2
        <span class="sidebar-close"><i class="bi bi-caret-left-fill"></i></span>
      </h1>

      <p>A responsive sidebar for mapping libraries like <a href="http://leafletjs.com/">Leaflet</a> or <a href="http://openlayers.org/">OpenLayers</a>.</p>

      <p class="lorem">Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet.</p>

      <p class="lorem">Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet.</p>

      <p class="lorem">Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet.</p>

      <p class="lorem">Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet.</p>
    </div>

    <div class="sidebar-pane" id="profile">
      <h1 class="sidebar-header">Profile<span class="sidebar-close"><i class="fa fa-caret-left"></i></span></h1>
    </div>

    <div class="sidebar-pane" id="messages">
      <h1 class="sidebar-header">Messages<span class="sidebar-close"><i class="fa fa-caret-left"></i></span></h1>
    </div>

    <div class="sidebar-pane" id="settings">
      <h1 class="sidebar-header">Settings<span class="sidebar-close"><i class="fa fa-caret-left"></i></span></h1>
    </div>
  </div>
</div>
<div id="map" class="sidebar-map"></div>
```

```js
const sidebar = L.control.sidebar('sidebar').addTo(map);
```

## Mapas silmultaneos

https://github.com/digidem/leaflet-side-by-side

https://gist.github.com/sigdeletras/8e14a1599d0ba5393d668c808f2b8061

```html
<script src="http://lab.digital-democracy.org/leaflet-side-by-side/leaflet-side-by-side.js"></script>
```

!!! question "Ejercicio 4 pts"
    Crear un archivo llamado ejercicios_leaflet.html y en este archivo crear un mapa de leaflet que contenga lo siguiente

    1. La capa base que sea el wms de topogris del ICGC. **(0.5 pt)**
    2. Cargar la capa Pob20k geojson de https://raw.githubusercontent.com/geostarters/dades/master/Pob20k.geojson **(0.5 pt)**
    3. Sobre la capa Pob20k hacer un temático (el campo para hacer el temático es libre) **(0.5 pt)**
    4. Cargar la capa Refugis geojson de https://raw.githubusercontent.com/geostarters/dades/master/refugis.geojson **(0.5 pt)**
    5. Aplicar un filtro a la Capa Refugis donde solo se muestren los refugios con una Cota superior a 2000 **(0.5 pt)**
    6. Agregar un icono personalizado a la capa de Refugis **(0.5 pt)**
    7. Al hacer click sobre un Refugio que se muestre la información del refugio: Nombre, Cota y Tipo **(0.5 pt)**
    8. Agregar un nuevo botón al mapa que haga zoom sobre el Pedraforca **(0.5 pt)**
