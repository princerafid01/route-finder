<template>
    <div class="container">
        <input type="search" id="address-input" placeholder="Where are we going?" class="search-location"
        onfocus="value = ''" ref="autocomplete"/>
        <button class="btn btn-info" @click.prevent="showDi" v-if="showButton">{{ text }}</button>
        <div id="hello"></div>
    </div>
</template>

<script>
import shp from "shpjs";
import L from "leaflet";
import jQuery from "jquery";
import places from "places.js";
// import google from "google-maps";
import data from "./data";
import slum from "./slum.json";
import 'leaflet-routing-machine';

export default {
    name: "HelloWorld",
    data: function() {
        return {
            s: "",
            text:'Show Direction',
            showButton: false,
            map:'',
            selected_lat: '',
            selected_lng: '',
            mapData: {
                type: "FeatureCollection",
                features: [
                    {
                        type: "Feature",
                        properties: {},
                        geometry: {
                            type: "Point",
                            coordinates: [90.41012048721313, 23.75251074332713]
                        }
                    },
                    {
                        type: "Feature",
                        properties: {},
                        geometry: {
                            type: "Point",
                            coordinates: [90.40949821472168, 23.754297977874888]
                        }
                    },
                    {
                        type: "Feature",
                        properties: {},
                        geometry: {
                            type: "Point",
                            coordinates: [90.40196657180786, 23.74815057370012]
                        }
                    },
                    {
                        type: "Feature",
                        properties: {},
                        geometry: {
                            type: "Point",
                            coordinates: [90.41338205337524, 23.74322065652415]
                        }
                    },
                    {
                        type: "Feature",
                        properties: {},
                        geometry: {
                            type: "LineString",
                            coordinates: [
                                [90.40949821472168, 23.75451401555371],
                                [90.4021167755127, 23.748543387758623],
                                [90.4136610031128, 23.743593844033676],
                                [90.4103136062622, 23.752667863831423],
                                [90.40932655334473, 23.754592574620776]
                            ]
                        }
                    }
                ]
            }
        };
    },
    props: {
        msg: String
    },
    methods:{
        showDi(){
            if(this.text == 'Show Direction'){
                this.text = 'Hide Direction';
            } else {
                this.text = 'Show Direction';
            }
            let links = document.querySelectorAll('.leaflet-right');
            if(links){
                [].forEach.call(links, (item) => {
                    item.classList.toggle('none');
                });
            }
        }
    },
    mounted() {
        this.$nextTick(function() {
            // Google api autocomplete
            // this.autocomplete = new google.maps.places.Autocomplete(
            //     (this.$refs.autocomplete), {types: ['geocode']}
            // );
            // this.autocomplete.addListener('place_changed', () => {
            // let place = this.autocomplete.getPlace();
            // let ac = place.address_components;
            // this.selected_lat = place.geometry.location.lat();
            // this.selected_lng = place.geometry.location.lng();
            // let city = ac[0]["short_name"];

            // console.log(`The user picked ${city} with the coordinates ${lat}, ${lon}`);
            // });
            // Google api ends for auto completing

            // Algolia api for autocompleting
            let placesAutocomplete = places({
                appId: 'plBUJSDMOSW1',
                apiKey: '29b31d34608d6f4449699fc4aa6ce2bf',
                container: document.querySelector('#address-input')
            });
            placesAutocomplete.configure({
                countries: ['bd'] // only search in the Bangladesh, the rest of the settings are unchanged: we're still searching for cities in German.
            })
            placesAutocomplete.on('clear', e => {
                if(this.map) {
                    this.map.remove();
                }
                this.showButton = false;
            });
            placesAutocomplete.on('change', e => {

                this.selected_lat = e.suggestion.latlng.lat;
                this.selected_lng = e.suggestion.latlng.lng;
                navigator.geolocation.getCurrentPosition((location) => {
                let lat = location.coords.latitude;
                let lng = location.coords.longitude;

                this.map = L.map('hello').setView([23.777176, 90.399452], 5);;

                L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}{r}.png', {
                    attribution: '© OpenStreetMap contributors'
                }).addTo(this.map);
                if(this.selected_lat){
                    let links = document.querySelectorAll('.leaflet-right');
                    if(links){
                        [].forEach.call(links, (item) => {
                        item.classList.add('none');
                        });
                        this.showButton = true;
                    }
                    L.Routing.control({
                        waypoints: [
                            L.latLng(lat,lng),
                            L.latLng(this.selected_lat, this.selected_lng),
                        ],
                        lineOptions: {
                            styles: [
                                { color: 'black', opacity: 0.15, weight: 9 },
                                { color: 'white', opacity: 0.8, weight: 6 },
                                { color: 'steelblue', opacity: 1, weight: 4 }
                            ]
                        },
                        altLineOptions: {
                            styles: [
                                { color: 'black', opacity: 0.15, weight: 9 },
                                { color: 'white', opacity: 0.8, weight: 6 },
                                { color: 'hotpink', opacity: 1, weight: 4 }
                            ]
                        }
                    }).addTo(this.map);
                }
                });
            });


        });
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
    margin: 40px 0 0;
}
ul {
    list-style-type: none;
    padding: 0;
}
li {
    display: inline-block;
    margin: 0 10px;
}
a {
    color: #42b983;
}
#hello {
    width: 100%;
    height: 480px;
}
.search-location {
      display: block;
    width: 60vw;
    margin: 0 auto;
    margin-top: 5vw;
    font-size: 14px;
    font-weight: 400;
    outline: none;
    height: 30px;
    line-height: 30px;
    text-align: center;
    border-radius: 5px;
    padding: 27px;
    margin-bottom: 3vw;
}
.none {display: none;}
button.btn.btn-info {
    background-color:#44c767;
	border-radius:8px;
	border:1px solid #18ab29;
	cursor:pointer;
	color:#ffffff;
	font-family:Arial;
	font-size:14px;
	padding:8px 18px;
	text-decoration:none;
    margin: 0 auto;
    display: block;
    margin-bottom: 3vw;
}
</style>
