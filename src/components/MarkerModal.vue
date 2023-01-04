<template>
  <div id="modal">
    <div id="modalContent">
        <!-- inherit styles from parent -->
        <h4
          :style="{
              backgroundColor: customStyle.backgroundColor,
              color: customStyle.color
            }">
          {{ marker.name }}
        </h4>
        <table id="dataTable">
          <tbody>
            <tr>
              <th>Fuel:</th>
              <th>Percentage:</th>
            </tr>
            <!-- tr styling as proof of concept, mostly -->
            <tr
              v-for="data in sortedPositiveData"
              :key="data.fuel"
              :style="rowStyle(data.fuel)"
              >
              <td>{{ data.fuel }}</td>
              <td>{{ data.perc }}</td>
            </tr>
          </tbody>
        </table>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    marker: Object,
    customStyle: Object
  },
  computed: {
    sortedPositiveData () {
      // no 0 data; also sort by percentage
      const info = []
      for (let el of this.marker.data.generationmix) {
        if (el.perc > 0) {
          info.push({
            fuel: el.fuel,
            perc: el.perc
          })
        }
      }
      return info.sort((a, b) => b.perc - a.perc)
    }
  },
  methods: {
    rowStyle (fuel) {
      let backgroundColor, color
      switch (fuel) {
        case "wind":
          backgroundColor = 'lightcyan'
          color = 'black'
          break;
        case "hydro":
          backgroundColor = 'aquamarine'
          color = 'black'
          break;
        case "gas":
          backgroundColor = 'crimson'
          color = 'white'
          break;
        case "nuclear":
          backgroundColor = 'yellowgreen'
          color = 'black'
          break;
        case "imports":
          backgroundColor = 'tomato'
          color = 'white'
          break;
        case "biomass":
          backgroundColor = 'teal'
          color = 'white'
          break;
        default:
          backgroundColor = 'lightslategrey'
          color = 'white'
          break;
      }
      return {
        backgroundColor, color
      }
    }
  }
}
</script>

<style lang="scss" scoped>
#modal {
  background-color: white;
  padding:  5px;
  h4 {
    text-align: center;
    padding: 5px 0;
  }
}

#dataTable {
  text-align: center;
  tr {
    &:first-of-type {
      background-color: gainsboro;
      color: #232323;
    }
    th, td {
      padding: 3px 10px;
    }
  }
}
</style>
