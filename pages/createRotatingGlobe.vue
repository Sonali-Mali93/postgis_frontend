<template>
    <div>
        <main class="w-screen h-screen">
            <v-map class="w-full h-full" :options="state.map" @loaded="onMapLoaded" />
            <button id="btn-spin">Pause rotation</button>
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
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import '@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css';

mapboxgl.accessToken =
    "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ";
const state = reactive({
    map: {
        container: "map",
        style: "mapbox://styles/mapbox/satellite-streets-v11",
        // center: [444.04931277036667, 26.266912177018096] as number[],
        zoom: 11,
        center: [30, 50.0],
        projection: 'globe'
    },
    data: []
});

function onMapLoaded(map: mapboxgl.Map) {

    map.on('style.load', () => {
        map.setFog({}); // Set the default atmosphere style
    });

    // The following values can be changed to control rotation speed:

    // At low zooms, complete a revolution every two minutes.
    const secondsPerRevolution = 120;
    // Above zoom level 5, do not rotate.
    const maxSpinZoom = 5;
    // Rotate at intermediate speeds between zoom levels 3 and 5.
    const slowSpinZoom = 3;

    let userInteracting = false;
    let spinEnabled = true;

    function spinGlobe() {
        const zoom = map.getZoom();
        if (spinEnabled && !userInteracting && zoom < maxSpinZoom) {
            let distancePerSecond = 360 / secondsPerRevolution;
            if (zoom > slowSpinZoom) {
                // Slow spinning at higher zooms
                const zoomDif =
                    (maxSpinZoom - zoom) / (maxSpinZoom - slowSpinZoom);
                distancePerSecond *= zoomDif;
            }
            const center = map.getCenter();
            center.lng -= distancePerSecond;
            // Smoothly animate the map over one second.
            // When this animation is complete, it calls a 'moveend' event.
            map.easeTo({ center, duration: 1000, easing: (n) => n });
        }
    }

    // Pause spinning on interaction
    map.on('mousedown', () => {
        userInteracting = true;
    });

    // Restart spinning the globe when interaction is complete
    map.on('mouseup', () => {
        userInteracting = false;
        spinGlobe();
    });

    // These events account for cases where the mouse has moved
    // off the map, so 'mouseup' will not be fired.
    map.on('dragend', () => {
        userInteracting = false;
        spinGlobe();
    });
    map.on('pitchend', () => {
        userInteracting = false;
        spinGlobe();
    });
    map.on('rotateend', () => {
        userInteracting = false;
        spinGlobe();
    });

    // When animation is complete, start spinning if there is no ongoing interaction
    map.on('moveend', () => {
        spinGlobe();
    });

    document.getElementById('btn-spin').addEventListener('click', (e) => {
        spinEnabled = !spinEnabled;
        if (spinEnabled) {
            spinGlobe();
            e.target.innerHTML = 'Pause rotation';
        } else {
            map.stop(); // Immediately end ongoing animation
            e.target.innerHTML = 'Start rotation';
        }
    });

    spinGlobe();
    //Add a vector tile source
    map.addControl(
        new MapboxGeocoder({
            accessToken: mapboxgl.accessToken,
            mapboxgl: mapboxgl,
        })
    );
    //Map Navigation Control
    map.addControl(new mapboxgl.NavigationControl(), 'top-left');


}
  //}
</script>
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

.marker {
    background-image: url("http://localhost:3000/assets/images/download.png");
    background-size: cover;
    width: 30px;
    height: 30px;
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
    margin-top: 600px;
}

#btn-spin {
    font: bold 12px/20px 'Helvetica Neue', Arial, Helvetica, sans-serif;
    background-color: #3386c0;
    color: #fff;
    position: absolute;
    top: 20px;
    left: 50%;
    z-index: 1;
    border: none;
    width: 200px;
    margin-left: -100px;
    display: block;
    cursor: pointer;
    padding: 10px 20px;
    border-radius: 3px;
}

#btn-spin:hover {
    background-color: #4ea0da;
}
</style>