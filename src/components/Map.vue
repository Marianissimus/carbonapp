<template>
  <div style="height: 80vh">
    <l-map
      id="map"
      :zoom="zoom"
      :min-zoom="minZoom"
      :center="center"
      :bounds="bounds"
      :max-bounds="maxBounds">
      <!-- Preloader -->
      <l-control
        v-if="isLoading && !error"
        position="topleft"
        class="overlay"
        id="loaderContainer">
        <div id="loader">
        </div>
        <div id="loaderText">
          Loading...
        </div>
      </l-control>
      <!-- Error -->
      <l-control
        v-if="error"
        position="topleft"
        class="overlay"
        >
        <div id="error">
          {{ error }}
        </div>
      </l-control>
      <l-tile-layer :url="url" />
      <!-- Map markers -->
      <CustomMarker
        v-for="marker in markers"
        :key="marker.id"
        :item.sync="marker"
        />
    </l-map>
    <div id="instructions" v-if="!isLoading">
      <h3>Carbon Intensity Map for GB</h3>
      <p>Click on each marker to see details</p>
    </div>
  </div>
</template>

<script>
import { latLngBounds } from "leaflet"
import { LMap, LTileLayer, LControl } from 'vue2-leaflet'
import { regions } from '@/assets/regions'
import CustomMarker from './Marker'
import axios from 'axios'

export default {
  name: 'Map',
  components: {
    LMap,
    LTileLayer,
    LControl,
    CustomMarker
  },
  data() {
    return {
      url: 'https://{s}.tile.osm.org/{z}/{x}/{y}.png',
      zoom: 7,
      minZoom: 5,
      center: [55, -4],
      markers: [...regions],
      bounds: latLngBounds([
        [50, -5],
        [59, 1]
      ]),
      maxBounds: latLngBounds([
        [45, -5],
        [63, 1]
      ]),
      error: null,
      isLoading: true
    }
  },
  mounted () {
    this.getCarbonData()
  },
  computed: {
    carbonDataUrls () {
     const baseUrl = 'https://api.carbonintensity.org.uk/regional/regionid/'
      const promises = []
      for (let item of this.markers) {
        promises.push(baseUrl + item.id)
      }
      return promises
    }
  },
  methods: {
    async getCarbonData () {
      /*  Promise.all (or the deprecated axios.all) would not help much in here, as we use each individual response; it would also reject with a single error */
      for (let promise of this.carbonDataUrls) {
        await axios.get(promise)
        .then(result => {
          let resData = result.data.data[0]
          let marker = this.markers.find(el => el.id === resData.regionid)
          marker.data = resData.data[0]
        })
        .catch(err => this.error = err)
      }
      if (!this.error) {
        this.isLoading = false
      }
    }
  }
}
</script>

<style scoped lang="scss">
#map {
  position: relative;
}
.overlay {
  top: 0;
  left: 0;
  height: 80vh;
  width: 100vw;
  margin: 0;
  background-color: rgba(255, 255, 255, .4);
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  position: absolute;
}
#loaderContainer {
  color: #FE5A5F;
  font-size: 25px;
  #loader {
    background:url(../assets/loadingmap.gif) center center no-repeat;
    width: 100px;
    height: 100px;
  }
}
#error {
  color: red;
  font-size: 30px;
}
</style>
