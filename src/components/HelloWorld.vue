<template>
    <div class="container">
        <input ref="autocomplete"
        placeholder="Search"
        class="search-location"
        onfocus="value = ''"
        type="text" />

        <div id="hello"></div>
    </div>
</template>

<script>
import shp from "shpjs";
import L from "leaflet";
// import google from "google-maps";
import data from "./data";
import slum from "./slum.json";
import 'leaflet-routing-machine';

export default {
    name: "HelloWorld",
    data: function() {
        return {
            s: "",
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
    mounted() {
        this.$nextTick(function() {
            // var mymap = L.map("hello").setView([23.777176, 90.399452], 11); // initialize map
            // L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
            //     attribution:
            //         '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
            // }).addTo(mymap); // add tile
            // L.Routing.control({
            // waypoints: [
            //     L.latLng(90.4103136062622, 23.752667863831423),
            //     L.latLng(90.40932655334473, 23.754592574620776)
            // ]
            // }).addTo(mymap);

            // var tear = new L.Icon({
            //     iconUrl: "../logo.png"
            // });
            // L.marker([23.777176, 	90.399452]).addTo(mymap).bindPopup('A pretty CSS3 popup.<br> Easily customizable.'); //add marker
            // L.geoJSON(data.responseJSON, {
            //   onEachFeature: function(feature, layer){
            //     layer.bindPopup('Slum');
            //     layer.setIcon(tear);
            //   }
            // }).addTo(mymap);
            // L.geoJSON(this.mapData, {
            //     style: function(feature) {
            //         return {
            //             color: "#000"
            //         };
			// 	}
            // }).addTo(mymap);
            this.autocomplete = new google.maps.places.Autocomplete(
                (this.$refs.autocomplete), {types: ['geocode']}
            );
            this.autocomplete.addListener('place_changed', () => {
            let place = this.autocomplete.getPlace();
            let ac = place.address_components;
            this.selected_lat = place.geometry.location.lat();
            this.selected_lng = place.geometry.location.lng();
            let city = ac[0]["short_name"];

            console.log(`The user picked ${city} with the coordinates ${lat}, ${lon}`);
            });
            navigator.geolocation.getCurrentPosition(function(location) {
                let lat = location.coords.latitude;
                let lng = location.coords.longitude;

            var map = L.map('hello').setView([23.777176, 90.399452], 11);;

            L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}{r}.png', {
                attribution: '© OpenStreetMap contributors'
            }).addTo(map);

            L.Routing.control({
                waypoints: [
                    L.latLng(lat,lng),
                    L.latLng(this.selected_lat, this.selected_lng),
                ],
                routeWhileDragging: true
            }).addTo(map);
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
  font-size: 20px;
  font-weight: 400;
  outline: none;
  height: 30px;
  line-height: 30px;
  text-align: center;
  border-radius: 10px;
}
</style>
