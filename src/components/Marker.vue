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
        class="infoBubble"
        :style="itemStyle"
        >
        <div class="forecastValue">{{ item.data.intensity.forecast }}</div>
      </div>
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
        offset: [0, 46]
      },
      forecastIconAnchor: [20, 0]
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
        style.backgroundColor = 'greenyellow'
        style.color = 'black'
      } else if (intensity <= 250) {
        style.backgroundColor = 'khaki'
      } else if (intensity <= 700) {
        style.backgroundColor = 'chocolate'
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
.infoBubble {
  width:20px;
  height:20px;
  padding:10px;
  -moz-transform:rotate(45deg);
  -webkit-transform:rotate(45deg);
  border-radius:30px;
  border-bottom-right-radius:0;
}

.infoBubble .forecastValue {
  -moz-transform:rotate(-45deg);
  -webkit-transform:rotate(-45deg);
}

</style>
