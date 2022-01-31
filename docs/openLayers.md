# OpenLayers

**OpenLayers** facilita la creación de un mapas dinámicos en cualquier página web. Puede mostrar mosaicos de mapas, datos vectoriales y marcadores cargados desde cualquier fuente. OpenLayers se ha desarrollado para promover el uso de información geográfica de todo tipo. Es JavaScript de código abierto completamente gratuito, publicado bajo la licencia BSD de 2 cláusulas (también conocida como FreeBSD). [^1]

Es una librería JavaScript orientada a objetos. Usar la librería implica crear objetos de las clases que proporciona e interactuar con ellos. Es necesario conocer los objetos principales y acudir a los [manuales de referencia](https://openlayers.org/en/latest/apidoc/) [^2]

## Mi primer mapa

Crear un archivo llamado openlayers.html y copiar lo siguiente:

``` html
<!DOCTYPE html>
<html lang="es">
  <head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/openlayers/openlayers.github.io@master/en/v6.12.0/css/ol.css" type="text/css">
    <style>
      .map {
        height: 600px;
        width: 100%;
      }
    </style>
    <script src="https://cdn.jsdelivr.net/gh/openlayers/openlayers.github.io@master/en/v6.12.0/build/ol.js"></script>
    <title>Ejemplo OpenLayers</title>
  </head>
  <body>
    <h2>Mi primer mapa</h2>
    <div id="map" class="map"></div>
    <script type="text/javascript">
      const map = new ol.Map({
        target: 'map',
        layers: [
          new ol.layer.Tile({
            source: new ol.source.OSM()
          })
        ],
        view: new ol.View({
          center: ol.proj.fromLonLat([1.5, 45.1]),
          zoom: 9
        })
      });
    </script>
  </body>
</html>
```

Resumen de pasos

1. Incluir las librerías de OpenLayers
2. Crear un contenedor DIV donde dibujar el mapa
3. Declarar una variable global a la que asignar el mapa
4. Definir una función de inicialización del mapa
    1. Crear un mapa (objeto ol.Map)
    2. Crear capas (objeto ol.Layer)
    3. Añadir capas al mapa
    4. Posicionar la vista del mapa

## Principales objetos

### El mapa y la vista

**ol/Map** es la clase principal de OpenLayers ya que define el mapa. Se representa en un contenedor de destino *terget* (por ejemplo, un elemento div en la página web que contiene el mapa). Todas las propiedades del mapa se pueden configurar en el momento de la construcción o mediante métodos de establecimiento, por Ej. setTarget(). 

Para crear un mapa 

```js
const map = new ol.Map(options); 
```

*options* son las opciones de configuración del mapa en forma de pares de claves y valores separados por comas. Por ejemplo

``` js
{
  view: new View({
    center: [0, 0],
    zoom: 1
  }),
  layers: [
    new TileLayer({
      source: new OSM()
    })
  ],
  target: 'map'
}
```

**ol/View** representa una vista simple 2D del mapa. El mapa no es responsable de cosas como el centro, el nivel de zoom y la proyección del mapa. En cambio, estas son propiedades de una instancia ol/View. 

Para crear una vista


```js
const view = new ol.View(options); 
```

Donde *options* son las opciones de la vista. Las opciones principales son:

* center: coordenadas iniciales del centro de la vista 
* zoom: nivel de zoom inicial
* maxZoom: máximo nivel de zoom.
* minZoom: mínimo nivel de zoom.
* projection: sistema de referencia de la proyección del mapa. Por defecto EPSG:3857

### Controles

Los controles son los objetos que proporcionan información del mapa y posibilitan interaccionar con el propio mapa y las capas. Todos los controles se derivan de la clase **ol.Control** como por ejemplo: ol.Control.Zoom, ol.Control.Rotate.

Por defecto el mapa tiene los siguientes controles 

* ol/control/Zoom: botones para aumentar y disminuir el zoom
* ol/control/Rotate: permite rotar el mapa
* ol/control/Attribution: muestra la propiedad attribution de las capas

Para añadir controles al mapa se utilizan sus métodos addControl

Algunos de los principales controles son:

* ol.FullScreen: Barra de niveles de escala
* ol.MousePosition: Coordenadas del puntero.
* ol.OverviewMap: Mapa guía
* ol.ScaleLine: Escala del mapa
* ol.ZoomSlider: Zoom mediante barra
* ol.ZoomToExtent: Zoom a una extensión

### Diferentes tipos de capas y sus origenes

Para obtener datos remotos para una capa, OpenLayers usa subclases **ol/source/Source** que especifica el origen de la información de la capa. OpenLayers tiene disponibles *sources* para servicios de mosaicos de mapas gratuitos y comerciales como OpenStreetMap o Bing, para fuentes OGC como WMS o WMTS, y para datos vectoriales en formatos como GeoJSON o KML. 

Las capas son los objetos que permiten añadir información geográfica al mapa, es una representación visual de datos de una fuente *source*. Todos las capas se derivan de la clase **ol.Layer** como por ejemplo: ol.Layer.Tile, ol.Layer.Vector, ol.Layer.VectorTile.

Para añadir capas al mapa se utilizan sus métodos addLayer

OpenLayers clasifica las capas según: 

* Capas base y de superposición: Sólo hay una capa base visible en cada momento, mientras que de superposición puede haber todas las necesarias.

OpenLayers tiene cuatro tipos básicos de capas:

* ol/layer/Tile: renderiza fuentes que proporcionan imágenes en mosaico en cuadrículas que están organizadas por niveles de zoom para resoluciones específicas.
* ol/layer/Image: renderiza fuentes que proporcionan imágenes de mapas en extensiones y resoluciones arbitrarias.
* ol/layer/Vector: renderiza datos vectoriales del lado del cliente.
* ol/layer/VectorTile: renderiza los datos que se proporcionan como mosaicos vectoriales.

En las capas vectoriales la fuente de información no es una imagen (mapa renderizado) sino elementos geográficos (geometrías de tipo punto, línea o polígono) y es el navegador quien se encarga de dibujarlas utilizando las capacidades de dibujo vectorial (SVG, VML y canvas). En ella se gestiona una lista de *features* (**ol.Feature**) que se componen de:

* Geometría (**ol.geom.Geometry**)
* id: identificador
* Estilo de simbolización (**ol.style.Style**)

Uno de los puntos fuertes de OpenLayers es que puede trabajar con muchos tipos de capas y formatos vectoriales:

* Servicios OGC WMS, WFS, WCS y WMTS
* Cachés: WMTS, TileCache, XYZ (tipo Google) y ArcGIS Server
* Formatos vectoriales: GML, KML, GeoJSON, WKT, GPX, GeoRSS, Atom...

#### WMS

Podemos hacer peticiones a servicios WMS como una imagen simple usando *ImageLayer* como haciendo peticiones teseladas usando *TileLayer*

Ejemplo WMS teselado

```js
const inundacion500Teselada = new ol.layer.Tile({
    source: new ol.source.TileWMS({
        url:'https://servicios.idee.es/wms-inspire/riesgos-naturales/inundaciones?',
        params: {'LAYERS': 'NZ.Flood.FluvialT500','FORMAT': 'image/png','TRANSPARENT': 'true'}
    })
});

map.addLayer(inundacion500Teselada);
```

Ejemplo WMS imagen simple

``` js
const inundacion500Simple = new ol.layer.Image({
    source: new ol.source.ImageWMS({
        url:'https://servicios.idee.es/wms-inspire/riesgos-naturales/inundaciones?',
        params: {'LAYERS': 'NZ.Flood.FluvialT500','FORMAT': 'image/png','TRANSPARENT': 'true'}
    })
});

map.addLayer(inundacion500Simple);
```

#### GeoJSON

Los tres ingredientes importantes para trabajar con datos vectoriales:

* Un formato para leer y escribir datos serializados (GeoJSON en este caso **ol.format.GeoJSON**)
* Una fuente de vector para obtener los datos y administrar un índice espacial de características. (**ol.source.Vector**)
* Una capa vectorial para representar las entidades en el mapa (**ol.layer.Vector**)

Ejemplo de cargar de GeoJSON desde una URL.

``` js
const vectorSource = new ol.source.Vector({
    url: "https://raw.githubusercontent.com/geostarters/dades/master/fulls50M.geojson",
    format: new ol.format.GeoJSON()
});

const vectorLayer = new ol.layer.Vector({
    source: vectorSource,
    style: new ol.style.Style({
        stroke: new ol.style.Stroke({
        color: 'blue',
        lineDash: [4],
        width: 3,
        }),
        fill: new ol.style.Fill({
        color: 'rgba(0, 0, 255, 0.1)',
        }),
    }),
});

map.addLayer(vectorLayer);
```

### Proyecciones

OpenLayers permite hacer reproyecciones tanto de capas vectoriales como de capas ráster en el navegador. Esto es una gran ventaja ya que la tranformación de capas ráster en el cliente (navegador) permite consumir servicios en SRS que inicialmente no están soportados por el proveedor del servicio. Se puede decir que esta capacidad es un punto fuerte que lo diferencia de otras librerías de mapas.

La tranformación de las capas vectoriales ya es más común en otras librerías y permite consumir datos vectoriales que estan en un SRS diferente al definido en nuestro mapa.

Para hacer la reproyecciones OpenLayers usa la librería de Proj4.

#### Reproyección Raster

OpenLayers tiene la capacidad de mostrar datos ráster de WMS, WMTS, imágenes estáticas y muchas otras fuentes en un sistema de coordenadas diferente al que se entrega desde el servidor. La transformación de las proyecciones cartográficas de la imagen se realiza directamente en un navegador web. La vista en cualquier sistema de referencia de coordenadas compatible con Proj4js es posible y ahora se pueden combinar y superponer capas previamente incompatibles. 

Ejemplo: https://openlayers.org/en/latest/doc/tutorials/raster-reprojection.html

#### Reproyeccion Vector

Ejemplo: http://dev.openlayers.org/examples/using-proj4js.html

## Ejemplos

Podéis ver más ejemplos de código en https://openlayers.org/en/latest/examples/

También podéis ver un worlshop con ejemplos en https://openlayers.org/workshop/en/

!!! question "Ejercicio  pt"
    1. Preparar un mapa con la capa de OpenStreetMap centrada en Catalunya.
    2. Agregar al mapa el servicio WMS del ICGC de la ortofoto 25c.
    3. Añadir el control fullscreen
    4. Añadir los municipios de Catalunya en formato geojson. (https://raw.githubusercontent.com/geostarters/dades/master/Municipis_Catalunya_EPSG4326.geojson)
    5. Agregar el control de capas (https://github.com/walkermatt/ol-layerswitcher)

## Referencias

[^1]: https://openlayers.org/
[^2]: https://openlayers.org/en/latest/apidoc/