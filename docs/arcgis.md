# ArcGIS API for Javascript

La **ArcGIS API for JavaScript** permite crear aplicaciones web interactivas y tiene visualizaciones tanto en 2D como en 3D. Forma parte de todo el ecosistema ArcGIS de ESRI.

Al igual que la API de google está soportada por una gran empresa como lo és ESRI. Está integrado con los recursos de la platadforma de ArcGIS Online. Para acceder a los servicios de ArcGIS como por ejemplo el mapa base, geocodificador, routing, etc es necesario tener una API KEY y tienen un coste (aunque tienen uso gratis pero limitado).  

La referencia de la API la podemos ver en https://developers.arcgis.com/javascript/latest/

## Generar la API KEY

1. Ingresar en el dashbord https://developers.arcgis.com/dashboard/
2. Seleccionar la opción de ArcGIS API for JavaScript
3. Seleccionar la opción de "no quiero especificar"
4. Hacer click en el botón de nueva API KEY

## Mi primer mapa

Crear un archivo llamado arcgis.html y copiar lo siguiente:

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://js.arcgis.com/4.22/esri/themes/light/main.css">
    <style>
        html,
        body,
        #viewDiv {
          padding: 0;
          margin: 0;
          height: 100%;
          width: 100%;
        }
    </style>
    
    <script src="https://js.arcgis.com/4.22/"></script>
    <script>
        require(["esri/config","esri/Map", "esri/views/MapView"], function (esriConfig,Map, MapView) {

            esriConfig.apiKey = "YOUR_API_KEY";

            const map = new Map({
                basemap: "arcgis-topographic" // Basemap layer service
            });

            const view = new MapView({
                map: map,
                center: [2, 41.5], // Longitude, latitude
                zoom: 7, // Zoom level
                container: "viewDiv" // Div element
            });

        });
    </script>
</head>
<body>
    <div id="viewDiv"></div>

</body>
</html>
```

Una diferencia con las otras librerias de mapas que veremos es el concepto de cargar módulos. Podemos ver en nuestro código que se llama a la función require y que se le pasa una matriz como parámetro. Esta matriz contiene los módulos que queremos cargar. 

## Estilos disponibles

La API dispone de varios estilos (themes) para la interfaz de la aplicación

Podemos ver los estilo en https://developers.arcgis.com/javascript/latest/styling/#themes

## Algunos Widgets disponibles

https://developers.arcgis.com/javascript/latest/api-reference/

* BaseMap Toggle: permite al usuario final cambiar entre mapas base. https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-BasemapToggle.html
* LayerList: proporciona una forma de mostrar una lista de capas y activar / desactivar su visibilidad. https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-LayerList.html
* Legend: muestra etiquetas y símbolos para capas en un mapa. Las etiquetas y sus símbolos correspondientes dependen de los valores establecidos en la capa. La leyenda solo mostrará las capas y subcapas que son visibles en la vista. https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-Legend.html  
* Locate: anima la vista a la ubicación actual del usuario. https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-Locate.html
* Measurement: agrupa y administra múltiples herramientas de medición y le permite cambiar fácilmente entre ellas. https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-Measurement.html
* ScaleBar: muestra una barra de escala en el mapa o en un nodo HTML especificado. https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-ScaleBar.html
* Print: conecta su aplicación con un servicio de impresión para permitir la impresión del mapa. Aprovecha la funcionalidad de impresión cartográfica completa de alta calidad del lado del servidor mediante el servicio ExportWebMap de ArcGIS, que se puede configurar con plantillas de diseño personalizadas. https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-Print.html
* Editor: proporciona una experiencia de edición lista para usar para ayudar a optimizar la experiencia de edición dentro de una aplicación web. https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-Editor.html

## Tipos de capas

### Raster

* MapImageLayer: permite mostrar y analizar datos de subcapas definidas en un servicio de mapas, exportando imágenes en lugar de elementos. Las imágenes del servicio de mapas se generan dinámicamente en el servidor en función de una solicitud, que incluye un LOD (nivel de detalle), un cuadro delimitador, dpi, referencia espacial y otras opciones. Servicio que viene de  ArcGIS Server. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-MapImageLayer.html
* ImageryTileLayer: presenta datos ráster de un servicio de imágenes en teselas. Servicio que viene de  ArcGIS Server. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-ImageryTileLayer.html
* WebTileLayer: proporciona una forma sencilla de agregar mapas de teselas que no son de ArcGIS Server como una capa a un mapa. Esto corresponde a los servicios XYZ. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-WebTileLayer.html
* WMSLayer: se utiliza para crear capas basadas en OGC Web Map Services (WMS). **WMSLayer** inicialmente ejecuta una solicitud WMS GetCapabilities, que puede requerir CORS o una página de proxy. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-WMSLayer.html
* WMTSLayer: se utiliza para crear capas basadas en OGC Web Map Services (WMTS). Al igual que el *WMSLayer* el **WMTSLayer** inicialmente ejecuta una solicitud WMTS GetCapabilities, que puede requerir CORS o una página de proxy. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-WMTSLayer.html
* OpenStreetMapLayer: permite usar mapas base de OpenStreetMap. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-OpenStreetMapLayer.html

### Vector

* FeatureLayer: se puede crear a partir de un servicio de mapas o un servicio de entidades; Elementos del portal de ArcGIS Online o ArcGIS Enterprise; o de una variedad de funciones del lado del cliente. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-FeatureLayer.html
* CSVLayer: es una capa de puntos basada en un archivo CSV (.csv, .txt). CSV es un formato de archivo de texto sin formato que se utiliza para representar datos tabulares, incluidas características de puntos geográficos (latitud, longitud). Normalmente, la coordenada de latitud es el valor Y y la coordenada de longitud es el valor X. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-CSVLayer.html
* GeoRSSLayer: se usa para crear una capa basada en GeoRSS. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-GeoRSSLayer.html
* KMLLayer: se utiliza para crear una capa basada en un archivo KML (.kml, .kmz). https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-KMLLayer.html
* GeoJSONLayer: se utiliza para crear una capa basada en GeoJSON. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-GeoJSONLayer.html
* VectorTileLayer: accede a teselas de datos almacenados en caché y los presenta en formato vectorial. La forma en que se muestra VectorTileLayer está definida por la Especificación de estilo de Mapbox. La información de estilo de VectorTileLayer se almacena por separado de sus mosaicos. https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-VectorTileLayer.html

## Ejemplos

Podéis ver más ejemplos de código en https://developers.arcgis.com/javascript/latest/sample-code/ 

!!! question "Ejercicio 1.5 pts"
    1. Preparar un mapa con la capa Osm centrada en España. Referencia de las capas base: https://developers.arcgis.com/javascript/latest/api-reference/esri-Map.html#basemap **(0.5 pt)**
    2. Sobre el ejercicio anterior añadir el widget toggleMap para cambiar entre la capa Osm y la capa satellite. **(0.5 pt)**
    3. Añadir servicio WMS del ICGC de la ortofoto de 25c. **(0.5 pt)**

## Referencias

https://developers.arcgis.com/javascript/latest/
