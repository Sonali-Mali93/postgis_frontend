<template>
    <div>
        <main class="w-screen h-screen">
            <v-map class="w-full h-full" :options="state.map" @loaded="onMapLoaded" />
            <div id="menu">
                <input id="satellite-v9" type="radio" name="rtoggle" value="satellite" checked="checked" />
                <!-- See a list of Mapbox-hosted public styles at -->
                <!-- https://docs.mapbox.com/api/maps/styles/#mapbox-styles -->
                <label for="satellite-v9">satellite</label>
                <input id="light-v10" type="radio" name="rtoggle" value="light" />
                <label for="light-v10">light</label>
                <input id="dark-v10" type="radio" name="rtoggle" value="dark" />
                <label for="dark-v10">dark</label>
                <input id="streets-v11" type="radio" name="rtoggle" value="streets" />
                <label for="streets-v11">streets</label>
                <input id="outdoors-v11" type="radio" name="rtoggle" value="outdoors" />
                <label for="outdoors-v11">outdoors</label>
            </div>
        </main>
        <p>{{state.data}}</p>
    </div>
</template>
<script setup lang="ts">
// import "mapbox-gl/dist/mapbox-gl.css";
// import "v-mapbox/dist/v-mapbox.css";
///
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import '@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css';

mapboxgl.accessToken =
    "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ";
const state = reactive({
    map: {
        container: "map",
        style: 'mapbox://styles/mapbox/cjaudgl840gn32rnrepcb9b9g',
        zoom: 5,
        maxZoom: 22,
        center: [-121.403732, 40.492392]
    },
    data: []
});


function onMapLoaded(map: mapboxgl.Map) {
    map.addSource('national-park', {
        type: 'geojson',
        data: {
            type: 'FeatureCollection',
            features: [
                {
                    type: 'Feature',
                    geometry: {
                        type: 'Polygon',
                        coordinates: [
                            [
                                [-121.353637, 40.584978],
                                [-121.284551, 40.584758],
                                [-121.275349, 40.541646],
                                [-121.246768, 40.541017],
                                [-121.251343, 40.423383],
                                [-121.32687, 40.423768],
                                [-121.360619, 40.43479],
                                [-121.363694, 40.409124],
                                [-121.439713, 40.409197],
                                [-121.439711, 40.423791],
                                [-121.572133, 40.423548],
                                [-121.577415, 40.550766],
                                [-121.539486, 40.558107],
                                [-121.520284, 40.572459],
                                [-121.487219, 40.550822],
                                [-121.446951, 40.56319],
                                [-121.370644, 40.563267],
                                [-121.353637, 40.584978]
                            ]
                        ]
                    }
                },
                {
                    type: 'Feature',
                    geometry: {
                        type: 'Point',
                        coordinates: [-121.415061, 40.506229]
                    }
                },
                {
                    type: 'Feature',
                    geometry: {
                        type: 'Point',
                        coordinates: [-121.505184, 40.488084]
                    }
                },
                {
                    type: 'Feature',
                    geometry: {
                        type: 'Point',
                        coordinates: [-121.354465, 40.488737]
                    }
                }
            ]
        }
    });


    //Map Navigation Control
    map.addControl(new mapboxgl.NavigationControl(), 'top-left');
    //Search Bar
    map.addControl(
        new MapboxGeocoder({
            accessToken: mapboxgl.accessToken,
            mapboxgl: mapboxgl,
        })
    );

}
//}
//   --------------------------------------------


</script>
<style>
.w-screen {
    width: 100vw;
}

.h-screen {
    height: 100vh;
}

.h-full {
    height: 100%;
}

.w-full {
    width: 100%;
}

.marker {
    background-image: url("http://localhost:3000/assets/images/download.png");
    background-size: cover;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    cursor: pointer;
}

.mapboxgl-popup {
    max-width: 200px;
}

.mapboxgl-popup-content {
    text-align: center;
    font-family: "Open Sans", sans-serif;
}

#menu {
    position: absolute;
    background: #EFEFEF;
    padding: 10px;
    font-family: "Open Sans", sans-serif;
}
</style>