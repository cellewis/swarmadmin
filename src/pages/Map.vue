<template>
  <q-page class="q-pa-sm">
    <div id="myMap" style="height: 90vh;"></div>
  </q-page>
</template>

<script>
import {defineComponent} from 'vue'
import {ref} from 'vue'

export default defineComponent({
  name: "Map",
  setup() {
    return {
      mapData: ref(''),

      initMap() {
        this.mapData = new google.maps.Map(document.getElementById('myMap'), {
          center: {lat: 10.669162108250172, lng: -61.513381617532836},
          zoom: 15
        })
      }
    }
  },
  mounted() {
    this.initMap();
  },
})
</script>
<style scoped>

</style>
