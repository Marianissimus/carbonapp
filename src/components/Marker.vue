<template>
  <l-marker :lat-lng="[item.lat, item.lng]">
    <!-- Tooltip with name location -->
    <l-tooltip
      :options="tooltipOptions">
      {{ item.name }}
    </l-tooltip>
    <!-- Custom to also represent carbon intensity. Uses css styling instead of multiple svg files for increased flexibility -->
    <l-icon
    v-if="item.data.intensity.forecast !== null"
    :icon-anchor="forecastIconAnchor"
    >
      <div
        class="dataDiv"
        :style="itemStyle"
        >
        <span>{{ item.data.intensity.forecast }}</span>
      </div>
      <div class="arrow" :style="{borderTopColor:  itemStyle.backgroundColor}"></div>
    </l-icon>
    <!-- generation mix table -->
    <l-popup v-if="item.data.generationmix">
     <modal :marker.sync="item" :customStyle="itemStyle" />
    </l-popup>
  </l-marker>
</template>

<script>
import { LMarker, LTooltip, LIcon, LPopup } from 'vue2-leaflet'
import Modal from './MarkerModal.vue'

export default {
  name: 'CustomMarker',
  props: {
    item: Object
  },
  components: {
    LMarker,
    LTooltip,
    LIcon,
    LPopup,
    Modal
  },
  data () {
    return {
      tooltipOptions: {
        direction: 'bottom',
        opacity: 1,
        offset: [3, 3]
      },
      forecastIconAnchor: [15, 37]
    }
  },
  computed: {
    itemStyle () {
      const intensity = this.item.data.intensity.forecast
      const style = {
        backgroundColor: 'white',
        color: 'black'
      }
      if (intensity <= 50) {
        style.backgroundColor = 'green'
        style.color = 'white'
      } else if (intensity <= 150) {
        style.backgroundColor = 'lime'
        style.color = 'black'
      } else if (intensity <= 250) {
        style.backgroundColor = 'yellow'
      } else if (intensity <= 700) {
        style.backgroundColor = 'red'
        style.color = 'white'
      } else {
        style.backgroundColor = 'darkred'
        style.color = 'white'
      }
      style.borderColor = style.backgroundColor
      return style
    }
  }
}
</script>

<style scoped>
.dataDiv {
  width:  30px;
  height: 30px;
  background-color: white;
  font-weight: bold;
  display:  flex;
  flex-direction: column;
  justify-content: center;
  border-width:  2px;
  border-style:  solid;
  border-radius: 50%;
  z-index: 2;
  position: relative;
}
/* classic css arrow trick */
.arrow {
  content: '';
  position: absolute;
  bottom: 0;
  left: 50%;
  width: 0;
  height: 0;
  border-left: 10px solid transparent;
  border-right: 10px solid transparent;
  border-top-width: 10px;
  border-top-style: solid;
  border-bottom: 10px solid transparent;
  right: 50%;
  bottom: -39px;
  z-index: 1;
}
</style>
