<template>
    <!-- Full Screen View -->
    <div class="main">
        <!-- Toggle Bar code -->
        <div class="left1 bg-gray">
            <br />
            <div class="">
                <!-- <h1 class="font-bold ml-2">Stored Events</h1> -->
                <br />
                <table class="">
                    <tr>
                        <th>Name</th>
                        <th>Desc</th>
                        <th>Toggle</th>
                    </tr>
                    <tr v-for="(val, index) in data.mapEvent" :key="val.name">
                        <td>{{ index }} ) {{ val.name }}</td>
                        <td>{{ val.desc }}</td>
                        <td>
                            <!--   v-model="info.checkedInfo"
                          :value="data.coordinates" -->
                            <!-- id="{{index}}"  v-bind:value="data.coordinates"  v-model="info.checkedInfo" -->
                            <input type="checkbox" name="dataEvent" @click="showDataOnMap($event, index)" />
                        </td>
                    </tr>
                </table>
            </div>
        </div>
        <!-- Map  -->
        <div class="right1">
            <v-map class="w-full h-full" :options="state.map" @loaded="getMapData">
                <div class=" form-control zIndex1 bg-slate-500 rounded-lg w-5/12" v-show="info.show">
                    <div class="p-2">
                        <table class="p-2">
                            <tr class="p-1">
                                <td class="p-1">
                                    <label class="label" for="name">Name</label>
                                </td>
                                <td>
                                    <input class="input" type="text" id="name" v-model="info.name" placeholder="Name" />
                                </td>
                            </tr>
                            <tr class="p-1">
                                <td class="p-1">
                                    <label class="label" for="desc">Description</label>
                                </td>
                                <td>
                                    <input class="input" type="text" id="desc" v-model="info.desc"
                                        placeholder="Description" />
                                </td>
                            </tr>
                            <tr class="p-1">
                                <td class="p-1">
                                    <label class="label" for="color">Select Color</label>
                                </td>
                                <td>
                                    <input type="color" id="color" v-model="info.color" />
                                </td>
                            </tr>
                            <tr class="p-1">
                                <td class="p-1">
                                    <button @click="submit" class="btn">
                                        Submit
                                    </button>
                                </td>
                                <td>
                                    <button @click="Cancel" class="btn">
                                        Cancel
                                    </button>
                                </td>
                            </tr>
                        </table>
                    </div>
                </div>
                -->
            </v-map>
        </div>
    </div>
    <!-- </main> -->
</template>
<script setup lang="ts">
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import "@mapbox/mapbox-gl-draw/dist/mapbox-gl-draw.css";
import MapboxDraw from "@mapbox/mapbox-gl-draw";
const data: any = reactive({
    map: {} as mapboxgl.Map,
    mapEvent: [],
    draw: {},
});
const state = reactive({
    map: {
        accessToken:
            "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw",
        style: "mapbox://styles/mapbox/streets-v11?optimize=true",
        center: [74.04931277036667, 18.266912177018096] as number[],
        zoom: 7,
        maxZoom: 22,
        container: "map",
    },
    api_data: []

});
let info = reactive({
    data1: {
        name: "",
        desc: "",
        color: "",
        geom: {},
    },
    //   arr: [],
    show: false,
    name: "",
    desc: "",
    color: "",
    geom: {},
});
async function getMapData(tempMap: mapboxgl.Map) {
    data.map = tempMap;
    mapboxgl.accessToken =
        "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw";
    const draw = new MapboxDraw({
        displayControlsDefault: false,
        controls: {
            polygon: true,
            trash: true,
        },
        defaultMode: "draw_polygon",
    });

    //Show the data through API

    getGisData();
    async function getGisData() {
        state.api_data = await $fetch("http://localhost:3001/map/geom");
        console.log("data from api: ", state.api_data);
        for (const feature of state.api_data) {
            console.log("this is come from loop")
            // create a HTML element for each feature
            const el = document.createElement("div");
            el.className = "marker";
            console.log("data from databse" + feature.geometry.coordinates);
            // make a marker for each feature and add to the map
            new mapboxgl.Marker(el)
                .setLngLat(feature.geometry.coordinates)
                .addTo(map);
            console.log("line 99");
        }
    }
    //end
    data.draw = draw;
    data.map.addControl(draw, "top-left");
    data.map.on("draw.create", updateData);
    //   data.map.on("draw.delete", updateData);
    // data.map.on("draw.update", updateData);
    async function updateData(e) {
        info.show = true;
        console.log(e);
        console.log(e.features[0].id);
        console.log("coordinates", e.features[0].geometry);
        info.geom = e.features[0];

        data.map.flyTo({
            // center: [],
            center: [e.features[0].geometry[0]],
            essential: true, // this animation is considered essential with respect to prefers-reduced-motion
        });
    }
}
// // this function is used to show and hide mapEvent
function showDataOnMap(e, index) {
    console.log(e.target.checked);
    console.log("data.layers ", data.mapEvent);
    if (e.target.checked == true) {
        let colorPick;
        let flyToLocation;
        let colorSelect = data.mapEvent.filter((e) => {
            if (e.geom.id == data.mapEvent[index].geom.id) {
                flyToLocation = data.mapEvent[index].geom;
                colorPick = e.color;
                return e.color;
            }
        });
        // Fly to a random location
        console.log("data fly to ", data)
        // data.map.flyTo({
        //     center: [(Math.random() - 0.5) * 360, (Math.random() - 0.5) * 100],
        //     // center: [e.features[0].geometry],
        //     essential: true, // this animation is considered essential with respect to prefers-reduced-motion
        // });

        console.log("selected flyToLocation", flyToLocation);
        console.log("selected color", colorSelect);
        console.log("Pick color", colorPick);
        console.log("checked", data.mapEvent[index].geom.id);
        const geometry: any = data.mapEvent[index].geom;
        console.log("geometry", geometry);
        // data.mapEvent.push(data1);
        let sId: string = data.mapEvent[index].geom.id;
        let randonNo = Math.round(Math.random() * 1e9);
        console.log("randonNo", randonNo);
        // data.map.addSource(sId, {
        //   type: "geojson",
        //   data: geometry,
        // });
        data.map.addSource(data.mapEvent[index].geom.id, {
            type: "geojson",
            data: geometry,
        });
        data.map.addLayer({
            //   id: randonNo,
            id: data.mapEvent[index].geom.id,
            //   source: sId,
            //   source: data.mapEvent[0].geom.id,
            source: data.mapEvent[index].geom.id,
            type: "fill",
            layout: {},
            paint: { "fill-color": colorPick, "fill-opacity": 0.3 },
        });
    } else {
        // data.map.removeSource(data.mapEvent[0].id);
        data.map.removeLayer(data.mapEvent[index].geom.id);
        data.map.removeSource(data.mapEvent[index].geom.id);
    }
    // console.log(data.mapEvent[index].geom.coordinates);
}
function submit() {
    info.data1 = {
        name: info.name,
        desc: info.desc,
        color: info.color,
        geom: info.geom,
    };
    console.log("data entered ", info.data1);
    data.mapEvent.push(info.data1);
    info.name = "";
    info.color = "";
    info.desc = "";
    info.geom = {};
    info.show = false;
    data.draw.deleteAll();
    //   data.map.removeLayer(data.polygon);

}
function Cancel() {
    info.show = false;
}
</script>
<style>
html,
body {
    margin: 0;
}

#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #452C50;
}

.w-screen {
    width: 100vw;
}

.main {
    height: 100vh;
    width: 100vw;
}

.left1 {
    height: 100vh;
    width: 25vw;
    float: left;
}

.right1 {
    height: 100vh;
    width: 75vw;
    float: right;
    position: relative;
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

.bg-gray {
    /* background-color: rgb(71, 97, 164); */
    background-color: whitesmoke
}

.zIndex {
    position: absolute;
    left: 0px;
    top: 0px;
    z-index: 1;
}

.zIndex1 {
    position: relative;
    left: 300px;
    top: 100px;
    z-index: 1;
}

.form-control {
    border: 1px solid white;
    position: absolute;
    left: 2;
    top: 60px;
    left: 500px;
    padding: 15px;
    border-radius: 5px;
    z-index: 2;
    background-color: lightskyblue;
}

.input {
    height: 20px;
    width: 150px;
    /* background-color: teal */
}

.label {
    font-weight: bold;
    color: black
}

.btn {
    border: 1px solid black;
    background: red;
    color: azure;
    height: 30px;
    width: 80px;
    left: 70px;
    font-weight: bold;
    border-radius: 5px;
    margin-top: 4px;
    align-items: center;
    justify-content: center
}
</style>