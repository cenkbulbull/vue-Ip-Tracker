<template>
  <div id="app">
    <div class="header-group w-100">
      <div class="form-div form-group d-flex align-items-center justify-content-center">
        <input @keydown.enter="searchIp()" v-model="query" class="search-input form-control w-50" placeholder="Search for any IP address or domain" />
        <button :disabled="query==''" @click="searchIp()" class="btn search-btn">Search</button>
      </div>
      <div class="info row">
        <div style="position: relative;" class="col-6 col-md-3 d-flex">
          <div class="w-100">
            <span>IP ADDRESS</span>
            <p>{{map.ip}}</p>
          </div>
          <div class="verticalline"></div>
        </div>
        <div style="position: relative;" class="col-6 col-md-3 d-flex">
          <div class="w-100">
            <span>LOCATION</span>
            <p>{{map.city}}/{{map.country}}</p>
          </div>
          <div class="verticalline"></div>
        </div>
        <div style="position: relative;" class="col-6 col-md-3 d-flex">
          <div class="w-100">
            <span>TIMEZONE</span>
            <p>{{map.timezone}}</p>
          </div>
          <div class="verticalline"></div>
        </div>
        <div style="position: relative;" class="col-6 col-md-3 d-flex">
          <div class="w-100">
            <span>ISP</span>
            <p class="text-center">{{map.isp}}</p>
          </div>
        </div>
      </div>
    </div>
    <div>
      <l-map style="height: 70vh;z-index:10" :zoom="zoom" :center="center">
        <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
        <l-marker :lat-lng="markerLatLng"></l-marker>
      </l-map>
    </div>
  </div>
</template>

<script>
  import L from 'leaflet';
  import { LMap, LTileLayer, LMarker } from 'vue2-leaflet';
  import axios from "axios"

  export default {
    components: {
      LMap,
      LTileLayer,
      LMarker
    },
    data () {
      return {
        url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
        attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
        zoom: 13,
        center: [0,0],
        markerLatLng: [0,0],
        query:'',
        map:{
          ip:null,
          country:null,
          city:null,
          isp:null,
          timezone:null
        }
      };
    },
    methods:{
      searchIp(){
        if (this.query!='') {
          axios.get(this.query)
          .then(response =>{
            //console.log(response.data)
            this.center = [response.data.lat,response.data.lon]
            this.markerLatLng = [response.data.lat,response.data.lon]

            this.map={
              ip:response.data.query,
              country:response.data.country,
              city:response.data.city,
              isp:response.data.isp,
              timezone:response.data.timezone
            }
          })
        }
      }
    },
    created(){
      axios.get()
      .then(response =>{
        //console.log(response.data)
        this.center = [response.data.lat,response.data.lon]
        this.markerLatLng = [response.data.lat,response.data.lon]

        this.map={
          ip:response.data.query,
          country:response.data.country,
          city:response.data.city,
          isp:response.data.isp,
          timezone:response.data.timezone
        }
      })
    }
  }
</script>

<style>
  /*
font-family: 'Work Sans', sans-serif;
font-family: 'Poppins', sans-serif;
*/
  @import url('https://fonts.googleapis.com/css2?family=Poppins&family=Work+Sans:wght@400;700&display=swap');

  *{
    font-family: 'Work Sans', sans-serif;
  }
  #app{
    height: 100vh;
  }
  .verticalline{
    width: 3px;
    height: 60px;
    border-left: 1px solid #000;
    opacity: .2;
    position: absolute;
    left: 100%;
  }
  .search-input{
    border-top-left-radius: 12px;
    border-bottom-left-radius: 12px;
    padding: 10px 20px;
  }
  .search-input:focus{
    box-shadow: 0 1px 1px rgba(0, 0, 0, 0.075) inset, 0 0 8px rgba(126, 239, 104, 0.6);
    outline: 0 none;
  }
  .search-btn{
    width: 100px;
    height: 45px;
    border: 0;
    font-size: 12px;
    border-top-right-radius: 12px;
    border-bottom-right-radius: 12px;
    background-color: #020300;
    color: #fff;
  }
  .header-group{
    position: relative;
    background-image: linear-gradient(to left, #4949A7 0%, #5E7FEC 100%);
    height: 30vh;
    padding-top: 40px;
  }
  .info{
    width: 80%;
    padding: 25px;
    position: absolute;
    z-index: 124;
    top: 60%;
    left:10%;
    background-color: #fff;
    border-radius: 14px;
    text-align: center;
  }
  .info div span{
    font-size: 10px;
    font-weight: bold;
    opacity: .5;
    letter-spacing: 1px;
  }
  .info div p{
    font-size: 14px;
    font-weight: bold;

  }
  @media only screen and (max-width: 767px) {
   .verticalline{
    left: 105%;
  }
  .info div:nth-child(2) .verticalline{
    opacity: 0;
  }
  .search-input{
    width: 90% !important;
    font-size: 12px;
  }
  .search-btn{
    width: 100px;
    height: 40px;
    font-size: 12px;
  }
}
</style>
