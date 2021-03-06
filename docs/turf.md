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
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.7.1/dist/leaflet.css" />
    
    <script src="https://unpkg.com/leaflet@1.7.1/dist/leaflet.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-hash/0.2.1/leaflet-hash.min.js" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Turf.js/6.3.0/turf.min.js" crossorigin="anonymous"></script>

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
            width: 30%;
            overflow: auto;
            background: rgba(255, 255, 255, 0.8);
            z-index: 10000;
        }
    </style>
</head>
<body>
    <pre id="features">
        <div>
            <select name="" id="objetivos">
                <option value="42.23993;1.70375">Pedraforca</option>
                <option value="41.6570;-0.8621">Zaragoza</option>
                <option value="37.8842;-4.7639">Córdoba</option>
                <option value="29.97684;31.13684">Giza Necropolis</option>
            </select>
        </div>
        <div>
            <span><img src="http://betaserver.icgc.cat/mapicons/apple.png" alt=""></span> Álvarez Sánchez, Albert: <span id="aa"></span>
        </div>
        <div>
            <span><img src="http://betaserver.icgc.cat/mapicons/2915103_1.png" alt=""></span> Conejo Rodríguez, Alvaro: <span id="ac"></span>
        </div>
        <div>
            <span><img src="http://betaserver.icgc.cat/mapicons/air_fixwing.png" alt=""></span> Heras Aixandri, Guillem las: <span id="gh"></span>
        </div>
        <div>
            <span><img src="http://betaserver.icgc.cat/mapicons/bulldozer.png" alt=""></span> Mele Francisco, Arnau: <span id="am"></span>
        </div>
        <div>
            <span><img src="http://betaserver.icgc.cat/mapicons/pyramid.png" alt=""></span> Navarro Lopez del Castillo, Arnau: <span id="an"></span>
        </div>
        <div>
            <span><img src="http://betaserver.icgc.cat/mapicons/pirates.png" alt=""></span> Peralta Resina, Xavier <span id="xp"></span>
        </div>
        <div>
            <button id="resultados">Resultados</button>
        </div>
    </pre>
    <div id='map'></div>

    <script>
        const map = L.map('map').setView([37.88437176085360, -4.779524803161621], 14);

        const hash = new L.Hash(map);

        // Capas base
        const osmLayer = L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap<\/a> contributors'
        }).addTo(map);

        const est = {
            aa: "",
            ac: "",
            gh: "",
            am: "",
            an: "",
            xp: ""
        };

        map.on('click', function(evt){

            console.log(evt.latlng);

            const objetivo = document.getElementById("objetivos").value.split(";");

            const pt1 = turf.point([objetivo[1], objetivo[0]]);
            const pt2 = turf.point([evt.latlng.lng, evt.latlng.lat]);
            const options = {units: 'kilometers'};
    
            const distance = turf.distance(pt1, pt2, options);

            console.log(distance);

            const key = Object.keys(est).find((key) => est[key] === "");
            if (key) {
                est[key] = {
                    distance: `${distance.toFixed(4)} Km`,
                    latlng: evt.latlng
                };
            }else {
                alert('Todos participaron');
            }
            

        });

        function crearIcono(id){
            let image = "http://betaserver.icgc.cat/mapicons/pin-export.png";
            switch (id) {
                case 'aa':
                    image = "http://betaserver.icgc.cat/mapicons/apple.png";
                    break;
                case 'ac':
                    image = "http://betaserver.icgc.cat/mapicons/2915103_1.png";
                    break;
                case 'gh':
                    image = "http://betaserver.icgc.cat/mapicons/air_fixwing.png";
                    break;
                case 'am':
                    image = "http://betaserver.icgc.cat/mapicons/bulldozer.png";
                    break;
                case 'an':
                    image = "http://betaserver.icgc.cat/mapicons/pyramid.png";
                    break;
                case 'xp':
                    image = "http://betaserver.icgc.cat/mapicons/pirates.png";
                    break;
                default:
                    break;
            }
            return L.icon({
                iconUrl: image,
                iconSize: [37, 37],
            });
        }

        function mostrarResultados() {
            Object.keys(est).forEach((key) => {
                if (est[key].latlng){
                    document.getElementById(key).innerHTML = est[key].distance;
                    L.marker(est[key].latlng, {icon: crearIcono(key)}).addTo(map);
                }
            });

            const objetivo = document.getElementById("objetivos").value.split(";");
            map.setView(objetivo, 15);
            L.marker(objetivo).addTo(map);

        }

        document.getElementById('resultados').addEventListener('click', mostrarResultados);

    </script>

</body>
</html>
```