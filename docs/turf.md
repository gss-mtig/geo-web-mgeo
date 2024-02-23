# Turf.js

Análisis geoespacial avanzado para navegadores y Node.js 

https://turfjs.org/

Ejemplo

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-hash/0.2.1/leaflet-hash.min.js" crossorigin="anonymous"></script>
    <script src="https://unpkg.com/@turf/turf@6/turf.min.js" crossorigin="anonymous"></script>
   
    <style>
        body {
            margin: 0;
            padding: 0;
        }
        #map {
            position: absolute;
            top: 0;
            bottom: 0;
            width: 100%;
        }
        #features {
            position: absolute;
            top: 0;
            right: 0;
            bottom: 0;
            width: 350px;
            overflow: auto;
            background: rgba(255, 255, 255, 0.8);
            z-index: 10000;
        }
    </style>
</head>
<body>
    <div id="features">
        <div>
            <select name="" id="objetivos">
            
            </select>
        </div>
        <div id="alumnos">

        </div>
        <div>
            <button id="resultados">Resultados</button>
        </div>
      </div>
    <div id='map'></div>

    <script>
        const map = L.map('map').setView([37.88437176085360, -4.779524803161621], 14);

        const objetivos = [
          {
            name: "Pedraforca",
            pos: "42.23993;1.70375",
            view: "9/41.7355/2.3703"
          },
          {
            name: "Zaragoza",
            pos: "41.6570;-0.8621",
            view: "8/41.098/-0.176"
          },
          {
            name: "Córdoba",
            pos: "37.8842;-4.7639",
            view: "7/39.880/-0.956"
          },
          {
            name: "Giza Necropolis",
            pos: "29.97684;31.13684",
            view: "9/30.1938/31.6187"
          },
          {
            name: "Great Wall of China",
            pos: "40.4316544;116.56464",
            view: "5/31.747/114.082"
          },
          {
            name: "Mt Everest",
            pos: "28.014503;86.90959",
            view: "6/31.812/92.340"
          },

        ]

        document.getElementById('objetivos').innerHTML = [`<option value="-----">Seleccionar objetivo</option>`, ...objetivos.map((item) => {
            return `<option value="${item.pos}@${item.view}">${item.name}</option>`;
        })].join("");

        const hash = new L.Hash(map);

        // Capas base
        const osmLayer = L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap<\/a> contributors'
        }).addTo(map);

        const satelite = L.tileLayer('https://api.maptiler.com/tiles/satellite-v2/{z}/{x}/{y}.jpg?key=YOUR_MAPTILER_API_KEY',{
          tileSize: 512,
          zoomOffset: -1,
          minZoom: 1,
          attribution: "\u003ca href=\"https://www.maptiler.com/copyright/\" target=\"_blank\"\u003e\u0026copy; MapTiler\u003c/a\u003e \u003ca href=\"https://www.openstreetmap.org/copyright\" target=\"_blank\"\u003e\u0026copy; OpenStreetMap contributors\u003c/a\u003e",
          crossOrigin: true
        }).addTo(map);

        function getColor(d) {
          return d > 15  ? '#cf597e' :
           d > 10  ? '#e88471' :
           d > 5  ? '#eeb479' :
           d > 3   ? '#e9e29c' :
           d > 2   ? '#9ccb86' :
           d > 1   ? '#39b185' :
                      '#009392';
        }

        const gridLayer = L.geoJSON(null, {
          style: function(feature){
            return {
              fillColor: getColor(feature.properties.count),
              weight: 2,
              opacity: 1,
              color: 'white',
              dashArray: '3',
              fillOpacity: feature.properties.count ? 0.7 : 0
            };
          }
        }).addTo(map);

        document.getElementById('objetivos').addEventListener("change", () => {
          if (document.getElementById("objetivos").value !== "-----") {
            const view = document.getElementById("objetivos").value.split("@")[1].split("/");
            map.setView([view[1], view[2]], view[0]);
            gridLayer.clearLayers();
            respMarkers.forEach(item => {
              item.remove();
            });
            est.forEach((item) => {
              if (item.latlng) item.latlng = null;
            });
          }
        });

        L.control.layers({
          osm: osmLayer,
          sat: satelite
        }, {}, {position: 'topleft'}).addTo(map);

        const est = [
          {
            id: "1",
            nom: "Puig Mora, Antoni",
            icon: "abduction",
            score: 0
          },
          {
            id: "2",
            nom: "Zaldua Antolinez, Carlos Eduardo",
            icon: "alien",
            score: 0
          },
          {
            id: "3",
            nom: "Durà Lahoz, Cristina",
            icon: "anon",
            score: 0
          },
          {
            id: "4",
            nom: "García Zárate, Diego",
            icon: "avalanche1",
            score: 0
          },
          {
            id: "5",
            nom: "Roselló Boada, Eva",
            icon: "battlefield",
            score: 0
          },
          {
            id: "6",
            nom: "Bueno Tricas, Gerard",
            icon: "blast",
            score: 0
          },
          {
            id: "7",
            nom: "Colàs Bajo, Marta",
            icon: "bomb",
            score: 0
          },
          {
            id: "8",
            nom: "Morales Rodriguez, Nicole",
            icon: "caraccident",
            score: 0
          },
          {
            id: "9",
            nom: "Timoner Alfageme, Paula",
            icon: "cowabduction",
            score: 0
          },
          {
            id: "10",
            nom: "Enguialle, Pierre",
            icon: "crimescene",
            score: 0
          },
          {
            id: "11",
            nom: "Rubí Perales, Pol",
            icon: "earthquake-3",
            score: 0
          },
          {
            id: "12",
            nom: "García Gómez, Rodrigo",
            icon: "fire",
            score: 0
          },
          {
            id: "13",
            nom: "Vintimilla Cruz, Tito Santiago",
            icon: "flood",
            score: 0
          },
          {
            id: "14",
            nom: "Miguel Novillo Fernandez",
            icon: "love_date",
            score: 0
          },
          {
            id: "15",
            nom: "Wang, Wenyu",
            icon: "peace",
            score: 0
          },
          {
            id: "16",
            nom: "Lyu, Xinyu",
            icon: "phantom",
            score: 0
          }/*,
          {
            id: "17",
            nom: "",
            icon: "pirates",
            score: 0
          },
          {
            id: "18",
            nom: "",
            icon: "planecrash",
            score: 0
          },
          {
            id: "19",
            nom: "",
            icon: "radiation",
            score: 0
          }*/
        ];

        const iconBaseUrl = "http://betaserver.icgc.cat/mapicons/";

        function createTable() {
          document.getElementById("alumnos").innerHTML = est.map(item => {
            return `<div>
              <input type="radio" name="estudiante" value="${item.id}">
              <span><img src="${iconBaseUrl}${item.icon}.png" alt=""></span> ${item.nom}: 
              <span>${item.distance ? `${item.distance.toFixed(4).replace('.', ',')} Km` : item.score}</span>
            </div>`;
          }).join("");
        }

        createTable();

        map.on('click', function(evt){


            const objetivo = document.getElementById("objetivos").value.split("@")[0].split(";");

            const pt1 = turf.point([objetivo[1], objetivo[0]]);
            const pt2 = turf.point([evt.latlng.lng, evt.latlng.lat]);
            const options = {units: 'kilometers'};

            const distance = turf.distance(pt1, pt2, options);

            const activeEstId = document.querySelector('input[name="estudiante"]:checked').value;

            const activeEst = est.find((item) => {
              return item.id === activeEstId;
            });

            activeEst.distance = distance;
            activeEst.latlng = evt.latlng

            const marker = L.marker(activeEst.latlng, {icon: crearIcono(activeEst.icon)}).addTo(map);

            setTimeout(() => {
              marker.remove()
            }, 1000);

        });

        function crearIcono(id){
          return L.icon({
              iconUrl: `${iconBaseUrl}${id}.png`,
              iconSize: [37, 37],
          });
        }

        const respMarkers = [];

        function mostrarResultados() {
            est.sort((a,b) => {
              if (!a.distance) return 1;
              if (!b.distance) return -1;
              return a.distance - b.distance
            });
            createTable();

            const respuestas = {
              type: "FeatureCollection",
              features: []
            }

            est.forEach((item) => {
              if (item.latlng) {
                const mark = L.marker(item.latlng, {icon: crearIcono(item.icon)}).addTo(map);
                respuestas.features.push({
                  type: "Feature",
                  geometry: {
                    "type": "Point",
                    "coordinates": [item.latlng.lng, item.latlng.lat]
                  }
                });
                respMarkers.push(mark);
              }
            });

            const objetivo = document.getElementById("objetivos").value.split("@")[0].split(";");
            map.setView(objetivo, 15);
            const mark = L.marker(objetivo).addTo(map);
            respMarkers.push(mark);
            
            const bboxpol = turf.bboxPolygon(turf.bbox(respuestas))
            const respArea = turf.transformScale(bboxpol, 2);
            const bbox = turf.bbox(respArea);
            const hexgrid = turf.hexGrid(bbox, 5);
            
            turf.featureEach(hexgrid, function (currentFeature, featureIndex) {
              const ptsWithin = turf.pointsWithinPolygon(respuestas, currentFeature);
              currentFeature.properties.count = ptsWithin.features.length;
            });

            gridLayer.addData(hexgrid);
        }
        
        document.getElementById('resultados').addEventListener('click', mostrarResultados);

    </script>

</body>
</html>
```