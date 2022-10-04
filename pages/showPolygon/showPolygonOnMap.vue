<template>
    <!-- Full Screen View -->
    <div class="main">
        <!-- Toggle Bar code -->
        <div class="left1 bg-gray">
            <div class="float-right"></div>
            <br />
            <div class="">
                <br />
                <table class="">
                    <tr>
                        <th>Name</th>
                        <th>Desc</th>
                        <th>Toggle</th>
                        <th>Opacity</th>
                        <th>Delete</th>
                    </tr>
                    <tr v-for="(val, index) in state.polygon" :key="val.name">
                        <td>{{ index + 1 }}) {{ val.name }}</td>
                        <td>{{ val.desc }}</td>
                        <td>
                            <!--   v-model="info.checkedInfo"
                          :value="data.coordinates" -->
                            <!-- id="{{index}}"  v-bind:value="data.coordinates"  v-model="info.checkedInfo" -->
                            <input type="checkbox" name="dataEvent" id="checkedData"
                                @click="GeoJSONDataToggle($event, index)" />
                        </td>
                        <td>
                            <!-- v-model="info.opacity" -->
                            <input type="range" @change="changeColorOpacity(index)" min="1" max="10"
                                v-model="info.opacity" />
                        </td>
                        <!-- <td>
                            <a @click="onDeleteOfProduct(val.id)">delete</a>
                        </td> -->
                    </tr>
                </table>
            </div>
        </div>
        <!-- Map  -->
        <div class="right1">
            <v-map class="w-full h-full" :options="state.map" @loaded="getMapData">
                <div class=" form-control" v-show="info.show">
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
import "@mapbox/mapbox-gl-draw/dist/mapbox-gl-draw.css";
import mapboxgl from "mapbox-gl";
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
        // center: [444.04931277036667, 26.266912177018096] as number[], //uses longitude, latitude
        center: [76.57470703125, 19.766703551716976] as number[],
        zoom: 4,
        maxZoom: 22,
        crossSourceCollisions: false,
        failIfMajorPerformanceCaveat: false,
        attributionControl: false,
        preserveDrawingBuffer: true,
        // container: "map",
    } as mapboxgl.MapboxOptions,
});
let info = reactive({
    data1: {
        name: "",
        desc: "",
        color: "",
        geom: "",
        geom1: {},
    },
    opacity: 1,
    show: false,
    name: "",
    desc: "",
    color: "",
    geom: {},
    changer: false,
    deleteIndex: null,
});
async function getMapData(tempMap: mapboxgl.Map) {
    data.map = tempMap;
    mapboxgl.accessToken =
        "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw";
    if (info.changer == true) {
        let i = info.deleteIndex;
        info.changer = false;
    }
    info.changer = false;
    var Draw = new MapboxDraw({
        displayControlsDefault: true,
        // Select which mapbox-gl-draw control buttons to add to the map.
        controls: {
            polygon: true,
            trash: true,
            point: true,
            line_string: true,
        },
        // Set mapbox-gl-draw to draw by default.
        // The user does not have to click the polygon control button first.
        defaultMode: "draw_polygon",
    });
    data.draw = Draw;
    data.map.addControl(Draw, "top-left");
    data.map.on("draw.create", updateData);
    async function updateData(e) {
        info.show = true;
        console.log(e);
        console.log(e.features[0].id);
        console.log(e.features[0].geometry);
        info.geom = e.features[0];
        info.data1.geom1 = e.features[0].geometry;
    }
    //data come from databse  amit//////////////////////////////////
    polygon();
    async function polygon() {
        state.polygon = await $fetch("http://localhost:3001/map/geom");
        console.log(state.polygon);
        console.log("data: ", state.polygon);
        // const featureCollection = {
        //     type: "geojson",
        //     data: {
        //         type: "FeatureCollection",
        //         features: [],
        //     },
        // };
        // featureCollection.data.features = state.polygon.map((element) => {
        //     return {
        //         type: "Feature",
        //         geometry: {
        //             type: "Polygon",
        //             coordinates: element.geom.coordinates,
        //         },
        //     };
        // });
        // data.map.addSource("polygon-data", featureCollection);
        // data.map.addLayer({
        //     id: "park-boundary",
        //     type: "fill",
        //     source: "polygon-data",
        //     paint: {
        //         "fill-color": "#A020F0",
        //         "fill-opacity": 0.4,
        //     },
        // });
    }
}
// // this function is used to show and hide mapEvent
// function showDataOnMap(e, index) {
//     console.log(e.target.checked);
//     console.log("data.layers ", data.mapEvent);
//     if (e.target.checked == true) {
//         info.changer = false;
//         var x;
//         let colorPick;
//         let flyToLocation;
//         let flyToLocation1;
//         let colorSelect = data.mapEvent.filter((e) => {
//             if (e.geom.id == data.mapEvent[index].geom.id) {
//                 flyToLocation = e.geom.geometry.coordinates[0];
//                 flyToLocation1 = e.geom.geometry.coordinates;
//                 colorPick = e.color;
//                 return e.color;
//             }
//         });
//         console.log("flyloc", flyToLocation);
//         console.log("typeData", data.mapEvent[index].geom);
//         let type = data.mapEvent[index].geom.geometry.type;
//         let latlngArr = [];
//         if (type == "LineString") {
//             console.log("It is LineString");
//             flyToLocation.map((e) => {
//                 console.log("e", e);
//                 latlngArr.push(e);
//             });
//         }
//         if (type == "Polygon") {
//             console.log("It is Polygon");
//             latlngArr = flyToLocation[0];
//         }
//         if (type == "Point") {
//             console.log("It is Point");
//             console.log("point data", flyToLocation1);
//             console.log("point data1", flyToLocation1[0]);
//             console.log("point data2", flyToLocation1[1]);
//             latlngArr = [flyToLocation1[0], flyToLocation1[1]];
//             x = new mapboxgl.Marker({
//                 draggable: true,
//                 color: colorPick,
//             })
//                 .setLngLat([latlngArr[0], latlngArr[1]])
//                 .addTo(data.map);
//         }
//         let latlng = flyToLocation[0];
//         console.log("latlng", latlng);
//         console.log("exact value latlng array ", latlngArr);
//         // Fly to a random location
//         data.map.flyTo({
//             center: [latlngArr[0], latlngArr[1]],
//             zoom: 6,
//             essential: true, // this animation is considered essential with respect to prefers-reduced-motion
//         });
//         console.log("selected color", colorSelect);
//         console.log("Pick color", colorPick);
//         console.log("checked", data.mapEvent[index].geom.id);
//         const geometry: any = data.mapEvent[index].geom;
//         console.log("geometry", geometry);
//         let sId: string = data.mapEvent[index].geom.id;
//         let randonNo = Math.round(Math.random() * 1e9);
//         console.log("randonNo", randonNo);
//         data.map.addSource(data.mapEvent[index].geom.id, {
//             type: "geojson",
//             data: geometry,
//         });
//         data.map.addLayer({
//             id: data.mapEvent[index].geom.id,
//             source: data.mapEvent[index].geom.id,
//             type: "fill",
//             layout: {},
//             paint: { "fill-color": colorPick, "fill-opacity": 0.5 },
//         });
//     } else {
//         data.map.removeLayer(data.mapEvent[index].geom.id);
//         data.map.removeSource(data.mapEvent[index].geom.id);
//         data.map.marker.remove(x);
//     }
// }
async function submit() {
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
    info.changer = false;
    let formData = {
        id: 1,
        Name: "",
        discription: "LineString",
        Geom: info.data1.geom1,
    };
    await $fetch("http://localhost:3001/map/geom", {
        method: "POST",
        body: JSON.stringify(info.data1),
    }).catch((err) => alert(err));
}
function changeColorOpacity(index) {
    console.log("opacity", index, info.opacity);
    let colorOpacity = info.opacity;
    let colorPick;
    let colorSelect = data.mapEvent.filter((e) => {
        if (e.geom.id == data.mapEvent[index].geom.id) {
            colorPick = e.color;
            return e.color;
        }
    });
    data.map.removeLayer(data.mapEvent[index].geom.id);
    data.map.removeSource(data.mapEvent[index].geom.id);
    data.map.addSource(data.mapEvent[index].geom.id, {
        type: "geojson",
        data: data.mapEvent[index].geom,
    });
    data.map.addLayer({
        id: data.mapEvent[index].geom.id,
        source: data.mapEvent[index].geom.id,
        type: "fill",
        layout: {},
        paint: { "fill-color": colorPick, "fill-opacity": colorOpacity / 10 },
    });
}
function GeoJSONDataToggle(e, index) {
    console.log(e, index);
    console.log("Checkbox clicked", state.polygon);
    if (e.target.checked == true) {
        console.log("index", state.polygon[index].geom);
        data.map.addSource(state.polygon[index].id, {
            type: "geojson",
            data: state.polygon[index].geom,
        });
        for (let i of state.polygon)
            if (i.geom.type == "Polygon") {
                data.map.addLayer({
                    id: state.polygon[index].id,
                    source: state.polygon[index].id,
                    type: "fill",
                    layout: {},
                    paint: {
                        // "fill-color": "#0000FF",
                        "fill-color": state.polygon[index].color,
                        "fill-opacity": 0.8,
                        // "fill-outline-color": "#000000"
                    },
                });

            }
            else if (i.geom.type == "LineString") {
                data.map.addLayer({
                    id: state.polygon[index].id,
                    source: state.polygon[index].id,
                    type: "line",
                    layout: {},
                    paint: {
                        "line-color": state.polygon[index].color
                    },
                });
            }

        // data.map.addLayer({
        //     id: state.polygon[index].id,
        //     source: state.polygon[index].id,
        //     type: "fill",
        //     layout: {},
        //     paint: {
        //         // "fill-color": "#0000FF",
        //         "fill-color": state.polygon[index].color,
        //         "fill-opacity": 0.8,
        //         "fill-outline-color": "#000000"
        //     },
        // });
    } else {
        data.map.removeLayer(state.polygon[index].id);
        data.map.removeSource(state.polygon[index].id);
    }
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
    color: rgb(62, 115, 221);
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
    width: 35vw;
    float: left;
}

.right1 {
    height: 100vh;
    width: 65vw;
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
    background-color: azure;
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
    /* padding: 25px;
 */
    padding-right: 20px;
    border-radius: 5px;
    z-index: 2;
    background-color: lightskyblue;
}

.input {
    height: 18px;
    width: 140px;
    margin: 1px;
}

.label {
    font-weight: bold;
    color: black;
    font-size: small;
    margin: 1px;

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