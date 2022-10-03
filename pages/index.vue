<template>

  <!-- <head> -->
  <link rel="stylesheet"
    href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-directions/v4.1.0/mapbox-gl-directions.css"
    type="text/css" />
  <!-- </head> -->
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
  </div>
</template>
<script setup lang="ts">
// import "mapbox-gl/dist/mapbox-gl.css";
// import "v-mapbox/dist/v-mapbox.css";
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import '@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css';

import MapboxDraw from "@mapbox/mapbox-gl-draw";
import "@mapbox/mapbox-gl-draw/dist/mapbox-gl-draw.css";
import MapboxDirection from "@mapbox/mapbox-gl-directions/dist/mapbox-gl-directions";
mapboxgl.accessToken =
  "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ";
const state = reactive({
  map: {
    container: "map",
    // accessToken:
    //   "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ",
    //style: "mapbox://styles/mapbox/streets-v11?optimize=true",
    style: "mapbox://styles/mapbox/satellite-streets-v11",
    // style: "https://basemaps.cartocdn.com/gl/dark-matter-gl-style/style.json",
    center: [444.04931277036667, 26.266912177018096] as number[],
    zoom: 11,
    maxZoom: 22,
  },
  api_data: []
});
var geojson = {
  type: "FeatureCollection",
  features: [
    {
      type: "Feature",
      properties: { title: "abc ", description: "back to home " },
      geometry: {
        type: "Point",
        coordinates: [444.04931277036667, 26.266912177018096],
      },
    },
    {
      type: "Feature",
      properties: { title: "villege", description: "uttar pradesh" },
      geometry: {
        type: "Point",
        coordinates: [444.0481862425804, 26.266565820518633],
      },
    },
    {
      type: "Feature",
      properties: { title: "market", description: "uttar pradesh." },
      geometry: {
        type: "Point",
        coordinates: [444.0496963262558, 26.266450368122516],
      },
    },
    {
      type: "Feature",
      properties: { title: "home", description: "asdfghjhjkl." },
      geometry: {
        type: "Point",
        coordinates: [444.04630064964294, 26.23214630354235],
      },
    },
  ],
};
//////////////////////////////////
function onMapLoaded(map: mapboxgl.Map) {
  getGisData();
  async function getGisData() {
    state.api_data = await $fetch("http://localhost:3001/post-gis-backend");
    console.log("data: ", state.api_data);
    for (const feature of state.api_data) {
      console.log("this is come from loop")
      // create a HTML element for each feature
      const el = document.createElement("div");
      el.className = "marker";
      console.log("data from databse" + feature.geography);
      // make a marker for each feature and add to the map
      new mapboxgl.Marker(el)
        .setLngLat(feature.geography.coordinates)
        .addTo(map);
      console.log("line 99");
    }
  }

  //Add marker on double click of mouse
  map.on("dblclick", (e) => {
    new mapboxgl.Marker({
      color: "#" + (Math.random().toString(16) + "87CEEB").substring(2, 8),
      draggable: true,
    })
      .setLngLat([e.lngLat.lng, e.lngLat.lat])
      .addTo(map);
    console.log(`A click event has occurred at ${e.lngLat}`);
  });
  const layerList = document.getElementById("menu");
  const inputs = layerList.getElementsByTagName("input");
  for (const input of inputs) {
    input.onclick = (layer) => {
      const layerId = layer.target.id;
      map.setStyle("mapbox://styles/mapbox/" + layerId);
    };
  }

  //show only one marker( ukrain )
  const marker = new mapboxgl.Marker({
    color: "#FFFFFF",
    draggable: true,
  })
    .setLngLat([30.5, 50.5])
    .addTo(map);


  // // Add directions to map
  // map.addControl(
  //   new MapboxDirection({
  //     accessToken: mapboxgl.accessToken,
  //   }),
  //   "top-left"
  // );
  //Search Bar
  map.addControl(
    new MapboxGeocoder({
      accessToken: mapboxgl.accessToken,
      mapboxgl: mapboxgl,
    })
  );

  //draw tool
  var Draw = new MapboxDraw();
  map.addControl(Draw, 'top-left');


  // //Map Navigation Control
  // map.addControl(new mapboxgl.NavigationControl(), 'top-left');

  // //Add a fullscreen control to a map
  // map.addControl(new mapboxgl.FullscreenControl(), 'bottom-left');




  map.addSource("Nagpur", {
    type: "geojson",
    data: {
      type: "Feature",
      geometry: {
        type: "Polygon",
        //<----boundary co-ordinate----->
        coordinates: [
          [
            [431.2188720703125, 23.75518176611264],
            [430.762939453125, 23.17066382710224],
            [431.729736328125, 23.160563309048314],
            [431.2188720703125, 23.75518176611264],
          ],
        ],
      },
    },
  });
  map.addLayer({
    id: "Nagpur",
    type: "fill",
    source: "Nagpur",
    layout: {},
    paint: {
      "fill-color": "#f02",
      //rediish
      "fill-opacity": 0.5,
    },
  });
  map.addLayer({
    id: "outline",
    type: "line",
    source: "Nagpur",
    layout: {},
    paint: {
      "line-color": "#000",
      //dark blackish
      "line-width": 3,
    },
  });
  polygon();
  async function polygon() {
    state.polygon = await $fetch("http://localhost:3001/map/geom");
    console.log(state.polygon);
    console.log("data: ", state.polygon);
    const featureCollection = {
      type: "geojson",
      data: {
        type: "FeatureCollection",
        features: [],
      },
    };
    featureCollection.data.features = state.polygon.map((element) => {
      return {
        type: "Feature",
        geometry: {
          type: "Polygon",
          coordinates: element.polygon.coordinates,
        },
      };
    });
    map.addSource("polygon-data", featureCollection);
    map.addLayer({
      id: "park-boundary",
      type: "fill",
      source: "polygon-data",
      paint: {
        "fill-color": " #F08",
        "fill-opacity": 0.4,
      },
    });
    map.addLayer({
      id: "outline",
      type: "line",
      source: "polygon-data",
      layout: {},
      paint: {
        "line-color": "#FF0000",
        //dark blackish
        "line-width": 5,
      },
    });
  }

}



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
}
</style>