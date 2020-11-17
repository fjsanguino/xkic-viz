<template>
  <div id="container">
    <ChangeLayer_button id="menu"/>
    <MapboxMap id="map"/>
  </div>

</template>

<script>
import MapboxMap from "@/components/MapBox.vue";
import ChangeLayer_button from "@/components/Button";

export default {

  name: 'app',
  components: {
    ChangeLayer_button,
    MapboxMap
  }
}
</script>

<style>
#menu {
  z-index: 10;
}

</style>