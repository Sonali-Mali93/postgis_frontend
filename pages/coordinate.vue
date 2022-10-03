<template>
    <div class="main-div">
        <div class="left-div">
            <!-- {{ state.arr }} -->
            <!-- {{inputData.details.name}}
            {{inputData.details.desc}} -->
            <input type="checkbox" name="" id="">{{ inputData.details.name }} {{ inputData.details.desc }}

        </div>
        <div class="right-div">
            <form v-if="state.flag" action="#" v-on:submit.prevent class="form-control1">
                <table>
                    <tr>
                        <td><label class="label1" for="name">Name:</label></td>
                        <td><input class="input1" v-model="inputData.details.name" id="nameid" type="text" name="name">
                        </td>
                    </tr>
                    <tr>
                        <td><label class="label1" for="name">Description:</label></td>
                        <td><input class="input1" v-model="inputData.details.desc" id="descid" type="text" name="desc">
                        </td>
                    </tr>
                    <tr>
                        <td><button class="btn1" type="submit" @click="SubmitForm">Submit</button></td>
                    </tr>
                </table>
            </form>
            <v-map class="w-full h-full" :options="state.map" @loaded="onMapLoaded">
            </v-map>
        </div>
    </div>
    <!-- </div> -->
</template>
<script setup lang="ts">
import MapboxDraw from "@mapbox/mapbox-gl-draw";
import "@mapbox/mapbox-gl-draw/dist/mapbox-gl-draw.css";
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import vMap from "v-mapbox";
import mapboxgl from "mapbox-gl";

mapboxgl.accessToken =
    "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ";
const state = reactive({
    map: {
        container: "map",
        style: "mapbox://styles/mapbox/satellite-streets-v11",
        center: [-100.04, 38.907] as number[],
        zoom: 2.5,
        maxZoom: 22,

    },
    flag: false,
    details: {
        name: "",
        desc: ""
    },
    arr: [],
});

const inputData = reactive({
    details: {
        name: "",
        desc: ""
    }

})

function onMapLoaded(map: mapboxgl.Map) {

    //Search box
    map.addControl(
        new MapboxGeocoder({
            accessToken: mapboxgl.accessToken,
            mapboxgl: mapboxgl,
        }),
        'top-left'
    );
    const draw = new MapboxDraw({
        displayControlsDefault: false,
        controls: {
            polygon: true,
            trash: true,
        },
        defaultMode: "draw_polygon",
    });
    map.addControl(draw, 'top-left');
    map.on("draw.create", updateArea);
    map.on("draw.delete", updateArea);
    map.on("draw.update", updateArea);

    async function updateArea(e) {

        // const data = draw.getAll();
        var x = draw.getAll();
        // let coordinates = e.features[0].geometry.coordinates;
        // let type1 = e.features[0].geometry.type;
        // let polygon = {

        //     type: `${type1}`,
        //     coordinates: `${coordinates}`,
        //     name: `${state.details.name}`,
        //     description: `${state.details.desc}`
        // };
        // console.log("polygon data", polygon)
        var x = draw.getAll();

        state.arr.push(x);
        state.arr.push(inputData.details)
        state.flag = true
        // state.arr.push(polygon)
        console.log("data after name and desc", state.arr)

        console.log(x["features"][0]["geometry"]["coordinates"]);
        console.log("polygon data  coordinates", x);
        // function btnClick() {
        //     // e.preventDefault();
        //     console.log("hello ")
        // }

        // btnClick()

    }

}
</script>
<style scoped>
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

.main-div {
    height: 100vh;
    width: 100vw;
}

.left-div {
    height: 100vh;
    width: 20vw;
    background-color: white;
    float: left;
}

.h-full {
    height: 100%;
}

.w-full {
    width: 60%;
}

.right-div {
    height: 100vh;
    width: 80vw;
    float: right;
}

.label {
    font-weight: bold
}

.form-control1 {
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

.input1 {
    height: 20px;
    width: 200px;
    /* background-color: teal */
}

.label1 {
    font-weight: bold;
    color: black
}

.btn1 {
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