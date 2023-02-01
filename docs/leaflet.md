# Leaflet

https://leafletjs.com/

## Pantalla completa 

https://leafletjs.com/examples/mobile/

## WMS

https://leafletjs.com/examples/wms/wms.html

## Markers

https://leafletjs.com/examples/custom-icons/

## Geojson

https://leafletjs.com/examples/geojson/

## Tematicos

https://leafletjs.com/examples/choropleth/

## Filtro

https://leafletjs.com/reference-1.7.1.html#geojson-filter


## Layer control

https://leafletjs.com/examples/layers-control/

## Hash

https://github.com/mlevans/leaflet-hash

``` html
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-hash/0.2.1/leaflet-hash.min.js" crossorigin="anonymous"></script>
```

## Eventos

https://leafletjs.com/reference-1.7.1.html#event-objects

``` js
var popup = L.popup();

function onMapClick(e) {
    popup
        .setLatLng(e.latlng) // Sets the geographical point where the popup will open.
        .setContent("Has hecho click en la coordenada:<br> " +  e.latlng.lat.toString() + "," +  e.latlng.lng.toString()) // Sets the HTML content of the popup.
        .openOn(map); // Adds the popup to the map and closes the previous one. 
}

map.on('click', onMapClick);
```

## Crear un control

https://leafletjs.com/examples/extending/extending-3-controls.html

### EasyButton

https://github.com/CliffCloud/Leaflet.EasyButton

``` html
<link href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/Leaflet.EasyButton/2.4.0/easy-button.min.css" crossorigin="anonymous" />

<script src="https://cdnjs.cloudflare.com/ajax/libs/Leaflet.EasyButton/2.4.0/easy-button.min.js" crossorigin="anonymous"></script>
```

## Context menu

https://github.com/aratcliffe/Leaflet.contextmenu


## Barra de edición

https://leaflet.github.io/Leaflet.draw/docs/leaflet-draw-latest.html

``` html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.draw/1.0.4/leaflet.draw.css" crossorigin="anonymous" />

<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet.draw/1.0.4/leaflet.draw.js" crossorigin="anonymous"></script>
``` 

``` js
// FeatureGroup is to store editable layers
const drawnItems = new L.FeatureGroup();
map.addLayer(drawnItems);
const drawControl = new L.Control.Draw({
    edit: {
        featureGroup: drawnItems
    }
});
map.addControl(drawControl);

map.on(L.Draw.Event.CREATED, function (e) {
    console.log(e);
    const type = e.layerType,
        layer = e.layer;
    if (type === 'marker') {
        // Do marker specific actions
    }
    // Do whatever else you need to. (save to db; add to map etc)
    drawnItems.addLayer(layer);
    console.log(layer.toGeoJSON());
});

map.on('draw:edited', function (e) {
    const layers = e.layers;
    console.log(layers);   
    layers.eachLayer(function (layer) {
        //do whatever you want; most likely save back to db
    });
});
```

## Editable

https://github.com/Leaflet/Leaflet.Editable

``` html
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-editable/1.2.0/Leaflet.Editable.min.js" crossorigin="anonymous"></script>
```

## Barra lateral

https://github.com/turbo87/sidebar-v2/

``` html
<link href="https://maxcdn.bootstrapcdn.com/font-awesome/4.1.0/css/font-awesome.min.css" rel="stylesheet">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/sidebar-v2@0.4.0/css/leaflet-sidebar.css">

<script src="https://cdn.jsdelivr.net/npm/sidebar-v2@0.4.0/js/leaflet-sidebar.js"></script>
```

## Mapas silmultaneos

https://github.com/digidem/leaflet-side-by-side

https://gist.github.com/sigdeletras/8e14a1599d0ba5393d668c808f2b8061

``` html
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
