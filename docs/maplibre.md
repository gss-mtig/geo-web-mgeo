# MapLibre GL JS (Mapbox GL)

**MapLibre GL JS** es una librería de JavaScript que utiliza WebGL para representar mapas interactivos a partir de mosaicos vectoriales y estilos MapLibre. Es parte del ecosistema MapLibre GL. [^1]

!!! warning
    En diciembre de 2020 Mapbox lanza la versión 2 de la librería y cambia la licencia y las condiciones de uso (Ahora es necesario tener un token de Mapbox para que funcione). Hasta la versión 1.13 la librería tiene una licencia BSD y se puede usar sin un token de Mapbox 

Debido al cambio de licencia parte de la comunidad lanza **MapLibre GL JS** [^2] que es un fork de Mapbox GL JS en su versión 1.13

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Maplibre</title>
    <link href='https://unpkg.com/maplibre-gl/dist/maplibre-gl.css' rel='stylesheet' />
    <script src='https://unpkg.com/maplibre-gl/dist/maplibre-gl.js'></script>
    <style>
        #map {
            position: absolute;
            top: 0;
            bottom: 0;
            width: 100%;
        }
    </style>
</head>
<body>
    <div id="map"></div>

    <script>
        const map = new maplibregl.Map({
            container: 'map',
            style: 'https://geoserveis.icgc.cat/contextmaps/icgc_mapa_estandard_general.json',                
            center: [2.16859, 41.3954],
            zoom: 13,
            maxZoom: 14,
            hash: true,
            pitch: 45
        });
    </script>
</body>
</html>
```

## Agregar capas y fuentes

### Sources (fuentes)

Ya vimos como agregar fuentes de datos en el estilo pero también podemos agregar fuentes de datos al mapa luego de cargar el estilo inicial del mapa.

Para agregar una fuete al mapa se usa el método **addSource**. 

Sintaxis

``` js
map.addSource("ID_DE_LA_FUENTE", opciones);
```

Ejemplo

``` js
map.addSource('muncat', {
    type: 'geojson',
    data: 'https://raw.githubusercontent.com/geostarters/dades/master/Municipis_Catalunya_EPSG4326.geojson'
});
```

!!! note
    Si el mapa tiene un estilo ya cargado las fuentes y las capas se deben cargar luego del el evento load del mapa que solo se llama la primera vez que se carga el mapa
    ``` js
    map.on('load', function() {
        // the rest of the code will go in here
        map.addSource('muncat', {
            type: 'geojson',
            data: 'https://raw.githubusercontent.com/geostarters/dades/master/Municipis_Catalunya_EPSG4326.geojson'
        });
    });
    ```

### Layers (capas)

Al igual que las fuentes de datos podemos agregar capas al mapa una vez cargado el estilo inicial del mapa. Para ellos usaremos el método **addLayer**

Sintaxis

``` js
map.addLayer(opciones);
```

Ejemplo

``` js
map.addLayer({
    'id': 'municipis',
    'type': 'fill',
    'source': 'muncat',
    'paint': {
        'fill-color': '#888888',
        'fill-opacity': 0.4
    },
    'filter': ['==', '$type', 'Polygon']
});
```

Las capas tienen dos subpropiedades que determinan cómo se procesan los datos de esa capa: propiedades de diseño y pintura.

*Layout properties*: Las propiedades de diseño aparecen en el objeto "layout" de la capa. Definen la estructura y organización de los elements en el mapa, como su visibilidad o la ubicación de etiquetas. Se aplican al principio del proceso de renderizado y definen cómo se pasan los datos de esa capa a la GPU. Los cambios en una propiedad de diseño requieren un paso de "diseño" asincrónico.

*Paint properties*: Las propiedades de pintura aparecen en el objeto "paint" de la capa. Controlan la apariencia visual de los elementos en el mapa, como colores, opacidad y tamaños. Las propiedades de la pintura se aplican más adelante en el proceso de renderizado. Los cambios en una propiedad de pintura son baratos y ocurren de forma sincrónica.

## Estilo

Un estilo Mapbox es un documento que define la apariencia visual de un mapa: qué datos dibujar, el orden para dibujarlos y cómo diseñar los datos al dibujarlos. Un documento de estilo es un objeto JSON con un nivel de raíz específico y propiedades anidadas. Esta especificación define y describe estas propiedades. [^3]


!!! note
    Al declarar un mapa no es necesario declarar el estilo

### Raíz (elementos del primer nivel)

Las propiedades de nivel raíz de un estilo Mapbox especifican las capas del mapa, las fuentes de datos y otros recursos, como por ejemplo los valores predeterminados para la posición inicial de la cámara.

Ejemplo

``` json
{
    "version": 8,
    "name": "NOMBRE_DEL_ESTILO",
    "sprite": "URL_DEL_SPRITE",
    "glyphs": "URL_DE_LAS_FUENTES/{fontstack}/{range}.pbf",
    "sources": {...},
    "layers": [...]
}
```

#### Sprite (iconos)

Un **sprite** es una imagen única que contiene todos los iconos incluidos en un estilo. Al combinar muchas imágenes pequeñas en una sola imagen (el objeto), puede reducir la cantidad de solicitudes necesarias para obtener todas las imágenes, lo que mejora el rendimiento y hace que su mapa se cargue más rápidamente.[^4]

Una URL base para recuperar la imagen y los metadatos del sprite. Las extensiones .png, .json y el factor de escala @ 2x.png se agregarán automáticamente. Esta propiedad es necesaria si alguna capa utiliza las propiedades patrón de fondo, patrón de relleno, patrón de línea, patrón de extrusión de relleno o imagen de icono. La URL debe ser absoluta

``` json
"sprite": "https://geoserveis.icgc.cat/contextmaps/sprites/sprite@1",
```

https://geoserveis.icgc.cat/contextmaps/sprites/sprite@1.png
https://geoserveis.icgc.cat/contextmaps/sprites/sprite@1.json


#### Glyphs (tipografías)

En tipografía, un **glifo** es una representación gráfica de un carácter.

La propiedad de glifos de un estilo proporciona una plantilla de URL para cargar conjuntos de glifos de campos de distancia firmados en formato PBF.

``` json
  "glyphs": "https://geoserveis.icgc.cat/contextmaps/glyphs/{fontstack}/{range}.pbf",
```

#### Sources

Objeto que define los orígenes de los datos.

Una fuente (**source**) indica qué datos debe mostrar el mapa. Se debe especifique el tipo de fuente con la propiedad "type". Lo tipos de fuentes deben ser: vector, raster, raster-dem, geojson, image, video.

https://maplibre.org/maplibre-style-spec/sources/

Ejemplo 

``` json
"sources": {
    "openmaptiles": {
      "type": "vector",
      "tiles": ["https://geoserveis.icgc.cat/servei/catalunya/contextmaps_v1/vt/{z}/{x}/{y}.pbf"]
    },
    "ortoICGC": {
      "type": "raster",
      "tiles": [
        "https://geoserveis.icgc.cat/icc_mapesmultibase/noutm/wmts/orto/GRID3857/{z}/{x}/{y}.jpeg"
      ],
      "tileSize": 256,
      "attribution": "<b>Ortofoto Catalunya</b>:<a href=\"https://www.icgc.cat/Aplicacions/Visors/ContextMaps\">Institut Cartogràfic i Geològic de Catalunya</a> |",
      "minzoom": 13.1,
      "maxzoom": 20
    }
}
```

#### Layers

Matriz que contiene las reglas de simbolización. El orden dentro de la matriz es importante ya que la forma en que se van dibujando, hace que la primera regla quede por debajo del todo y la última regla quede por encima del todo. De esta manera, la primera regla suele ser el color de fondo del mapa, y las últimas suelen ser la toponimia o los PoIs. El tipo de capa se especifica mediante la propiedad "type" y debe ser de fbackground, fill, line, symbol, raster, circle, fill-extrusion, heatmap, hillshade, sky.

https://maplibre.org/maplibre-style-spec/layers/

Ejemplo

``` json
"layers": [
    {
      "id": "background",
      "type": "background",
      "paint": {
        "background-color": "#F4F9F4"
      }
    },
    {
      "id": "ortoICGC",
      "type": "raster",
      "source": "ortoICGC",
      "minzoom": 13.1,
      "maxzoom": 19,
      "layout": {
        "visibility": "visible"
      }
    },
    {
      "id": "park-outline",
      "type": "line",
      "source": "openmaptiles",
      "source-layer": "park",
      "filter": [
        "==",
        "$type",
        "Polygon"
      ],
      "layout": {},
      "paint": {
        "line-color": {
          "base": 1,
          "stops": [
            [
              6,
              "hsla(96, 40%, 49%, 0.36)"
            ],
            [
              8,
              "hsla(96, 40%, 49%, 0.66)"
            ]
          ]
        },
        "line-dasharray": [
          3,
          3
        ]
      }
    },
    {
      "id": "landcover-grass-park",
      "type": "fill",
      "source": "openmaptiles",
      "source-layer": "park",
      "filter": [
        "==",
        "class",
        "public_park"
      ],
      "paint": {
        "fill-color": "#d8e8c8",
        "fill-opacity": 0.8
      }
    },
    ...
```

#### Otras propiedades

Habitualmente también se usan otras propiedades para indicar la vista inicial del mapa. Por ejemplo:

``` json
{
  ...
  "center": [2.15, 41.39],
  "zoom": 12,
  "bearing": -45,
  "pitch": 0
}
```

## Temáticos

Se puede definir el valor de cualquier propiedad de diseño, propiedad de pintura o filtro como una expresión.

Una expresión define una fórmula para calcular el valor de la propiedad utilizando los operadores que se describen a continuación.

Los operadores de expresión proporcionados por Mapbox GL incluyen:

* Operadores matemáticos para realizar operaciones aritméticas y otras operaciones con valores numéricos
* Operadores lógicos para manipular valores booleanos y tomar decisiones condicionales
* Operadores de cadenas para manipular cadenas
* Operadores de datos para proporcionar acceso a las propiedades de las características de origen
* Operadores de cámara para proporcionar acceso a los parámetros que definen la vista del mapa actual

Las expresiones se representan como matrices JSON. El primer elemento de una matriz de expresión es una cadena que nombra al operador de expresión, por ejemplo, "*" o "caso". Los elementos que siguen (si los hay) son los argumentos de la expresión. Cada argumento es un valor literal (una cadena, un número, booleano o nulo) u otra matriz de expresión.

``` json
[expression_name, argument_0, argument_1, ...]
```

https://maplibre.org/maplibre-style-spec/expressions/

Ejemplo

``` js
map.addLayer({
    'id': 'municipis',
    'type': 'fill',
    'source': 'muncat',
    'paint': {
    'fill-color': [
        'match',
        ['get', 'provincia'],
        '25',
        '#ff0000',
        '17',
        '#00ff00',
        '08',
        '#0000ff',
        '43',
        '#ff00ff',
        '#ccc'
    ],
    'fill-opacity': 0.4
    },
    'filter': ['==', '$type', 'Polygon']
});
```

## Filtros

Una expresión que especifica las condiciones de los features. Solo se muestran las features que coinciden con el filtro. Las expresiones de zoom en los filtros solo se evalúan en niveles de zoom enteros

Ejemplo

``` js
map.addLayer({
    'id': 'municipis',
    'type': 'fill',
    'source': 'muncat',
    'paint': {
    'fill-color': [
        'match',
        ['get', 'provincia'],
        '25',
        '#ff0000',
        '17',
        '#00ff00',
        '08',
        '#0000ff',
        '43',
        '#ff00ff',
        '#ccc'
    ],
    'fill-opacity': 0.4
    },
    'filter': [
        'all',
        ['==', '$type', 'Polygon'],
        ['==', ['get', 'provincia'], 25]
    ]
});
```

Otro ejemplo

``` js
'filter': [
    'all',
    ['==', '$type', 'Polygon'],
    ['in', 'provincia', '25', '17'],
]
```

## Cambiar estilo de elementos en el cliente

Para cambiar el estilo o la visualización de un elemento podemos usar los métodos **setPaintProperty** o **setLayoutProperty** dependiendo de que propiedad queremos actualizar.

La sintaxis es igual para los dos casos

``` js
map.setPaintProperty('LAYER_ID', 'NOMBRE_DE_LA_PROPIEDAD', 'VALOR');

map.setLayoutProperty('LAYER_ID', 'NOMBRE_DE_LA_PROPIEDAD', 'VALOR');
```

Ejemplo

``` js
map.setPaintProperty('my-layer', 'fill-color', '#faafee');

map.setLayoutProperty('my-layer', 'visibility', 'none');
```

``` html
<div>
    <input type="color" id="water-color">
    <label for='water-color'>Color del agua</label>
</div>
<div>
    <input type="checkbox" id="edificios" checked>
    <label for='edificios'>Edificios</label>
</div>
```

``` js
function cambiaColorAgua(evt) {
    const color = evt.target.value;
    console.log(color);
    map.setPaintProperty('water-ocean', 'fill-color', color);
}

function muestraEdificios(evt) {
    const isChecked = evt.target.checked;
    if(isChecked){
        map.setLayoutProperty('building-residential', 'visibility', 'visible');
    }else{
        map.setLayoutProperty('building-residential', 'visibility', 'none');
    }
}

document.getElementById("water-color").addEventListener("change", cambiaColorAgua, false);
document.getElementById("water-color").addEventListener("input", cambiaColorAgua, false);
document.getElementById("edificios").addEventListener("change", muestraEdificios, false);
```

## Cambiar estilo mantener datos

En Mapbox GL no hay el concepto de capas Base y de capas overlay, por el contrario todo pertenece al estilo. 

Cuando agregamos una capa al mapa la estamos agregando al estilo por lo tanto si cambiamos de estilo perderemos todas capas que hemos agregado al estilo.

Ejemplo

``` html
<style>
    #menu {
        position: absolute;
        z-index: 1000;
        background: #fff;
        padding: 10px;
        font-family: 'Open Sans', sans-serif;
        top: 5px;
        left: 5px;
        border-radius: 7px;
        -webkit-box-shadow: 5px 5px 5px -5px rgba(0, 0, 0, 0.75);
        -moz-box-shadow: 5px 5px 5px -5px rgba(0, 0, 0, 0.75);
        box-shadow: 5px 5px 5px -5px rgba(0, 0, 0, 0.75);
    }
</style>


<div id='menu'>
    <input id='icgc' type='radio' name='rtoggle' value='icgc_mapa_estandard_general' checked='checked'>
    <label for='icgc'>icgc</label>
    <input id='fulldark' type='radio' name='rtoggle' value='icgc_mapa_base_fosc'>
    <label for='fulldark'>fulldark</label>
</div>
```

``` js
document.getElementsByName('rtoggle').forEach((elem) => {
    elem.addEventListener("change", function(event) {
        const item = event.target.value;
        switchLayer(item);
    });
})

function switchLayer(layer) {
    map.setStyle('https://geoserveis.icgc.cat/contextmaps/' + layer + '.json');
}
```

Ahora si cambiamos de estilo vemos que hemos perdido la capa de los municipios. Para evitar esto podemos usar el evento **styledata** (que se lanzaz cuando el estilo del mapa se carga o cambia) en lugar del evento *load*. Debido a que la carga del estilo es asyncrona debemos esperar a que el estilo del mapa esté cargado por completo (*map.isStyleLoaded()*) para luego cargar nuestras capas.

``` js
/*
map.on('styledata', () => {
    const waiting = () => {
        if (!map.isStyleLoaded()) {
            setTimeout(waiting, 200);
        } else {
            cargarDatos();
        }
    };
    waiting();
});
*/

function switchLayer(layer) {
    map.once('styledata', () => {
        cargarDatos();
    });
    map.setStyle('https://geoserveis.icgc.cat/contextmaps/' + layer + '.json');
}

function cargarDatos() {

    //comprobamos que el source no existe para evitar cargar un mismo source 2 veces que da un mensaje de error
    if (!map.getSource('muncat')) {
        map.addSource('muncat', {
            type: 'geojson',
            data: 'https://raw.githubusercontent.com/geostarters/dades/master/Municipis_Catalunya_EPSG4326.geojson'
        });
    }else {
        const geojsonData = map.getSource('muncat')._data; // Guardar los datos actuales
        map.addSource('muncat', {
            type: 'geojson',
            data: geojsonData
        });
    }

    //la misma comprobación para las capas
    if (!map.getLayer('municipis')) {
        map.addLayer({
            'id': 'municipis',
            'type': 'fill',
            'source': 'muncat',
            'paint': {
            'fill-color': [
                'match',
                ['get', 'provincia'],
                '25',
                '#ff0000',
                '17',
                '#00ff00',
                '08',
                '#0000ff',
                '43',
                '#ff00ff',
                '#ccc'
            ],
            'fill-opacity': 0.4
            },
            'filter': [
                'all',
                ['==', '$type', 'Polygon'],
                ['in', 'provincia', '25', '17'],
            ]
        });
    }
}
```

## Eventos

### click

Opcionalmente se puede indicar el id de la capa en la que se quiere capturar el evento. Si no se especifica es en todo el mapa.

``` js
map.on('click', 'municipis', function (e) {
    new maplibregl.Popup()
    .setLngLat(e.lngLat)
    .setHTML(e.features[0].properties.nomn_muni)
    .addTo(map);
});
```

### mouseenter & mouseleave

En este caso siempre se debe especificar el id de la capa.

``` js
// Change the cursor to a pointer when the mouse is over the layer.
map.on('mouseenter', 'municipis', function () {
    map.getCanvas().style.cursor = 'pointer';
});

// Change it back to a pointer when it leaves.
map.on('mouseleave', 'municipis', function () {
    map.getCanvas().style.cursor = '';
});
```

### mousemove

Opcionalmente se puede indicar el id de la capa en la que se quiere capturar el evento. Si no se especifica es en todo el mapa.

``` html
<style>
    #features {
        position: absolute;
        top: 0;
        right: 0;
        bottom: 0;
        width: 50%;
        overflow: auto;
        background: rgba(255, 255, 255, 0.8);
    }
</style>

<pre id="features"></pre>
```

``` js
map.on('mousemove', function (e) {
    const features = map.queryRenderedFeatures(e.point);
    
    // Limit the number of properties we're displaying for
    // legibility and performance
    const displayProperties = [
    'type',
    'properties',
    'id',
    'layer',
    'source',
    'sourceLayer',
    'state'
    ];
    
    const displayFeatures = features.map(function (feat) {
        const displayFeat = {};
        displayProperties.forEach(function (prop) {
            displayFeat[prop] = feat[prop];
        });
        return displayFeat;
    });
    
    document.getElementById('features').innerHTML = JSON.stringify(
        displayFeatures,
        null,
        2
    );
});
```

Resaltar los municipios de la misma comarca

``` js
map.addLayer({
    'id': 'municipis-highlighted',
    'type': 'fill',
    'source': 'muncat',
    'paint': {
        'fill-outline-color': '#484896',
        'fill-color': '#6e599f',
        'fill-opacity': 0.75
    },
    // Display none by adding a
    // filter with an empty string.
    'filter': ['in', 'comarca', '']
});

map.on('mousemove', 'municipis', function (e) {
    // Use the first found feature.
    const feature = e.features[0];

    // Query the counties layer visible in the map.
    // Use filter to collect only results
    // with the same county name.
    const relatedFeatures = map.querySourceFeatures('muncat', {
        sourceLayer: 'municipis',
        filter: ['in', 'comarca', feature.properties.comarca]
    });
    
    console.log(relatedFeatures);

    // Add features with the same comarca
    // to the highlighted layer.
    map.setFilter('municipis-highlighted', [
        'in',
        'comarca',
        feature.properties.comarca
    ]);

});

map.on('mouseleave', 'municipis', function () {
    map.setFilter('municipis-highlighted', ['in', 'comarca', '']);
});
```

!!! question "Ejercicio 2.5 pts"
    1. Preparar un mapa interactivo de coropletas. Replicar el resultado final de este tutorial de Leaflet https://leafletjs.com/examples/choropleth/ pero usando MapLibre. **(2.5 pt)**

    Tip: mirar este ejemplo: https://maplibre.org/maplibre-gl-js/docs/examples/hover-styles/


## Más recursos

Tutorial rápido: https://geoinquiets.github.io/vt-hackato-atm/

Tutorial más completo: https://geoinquiets.github.io/taller-vt/

## Referencias

[^1]: https://maplibre.org/maplibre-gl-js/docs/API/
[^2]: https://maplibre.org/maplibre-gl-js/docs/API/
[^3]: https://maplibre.org/maplibre-style-spec/
[^4]: https://docs.mapbox.com/help/glossary/sprite/