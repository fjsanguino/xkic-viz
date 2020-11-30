<template>
  <div id="app">
    <el-container class="container">
      <el-aside width="210px">
        <Sidebar/>
      </el-aside>

      <el-container>


        <el-main>

          <MapboxMap id="map"/>
          <div class='map-overlay' id="legend"></div>


        </el-main>
      </el-container>
    </el-container>
  </div>
</template>


<script>

import Sidebar from "./components/Sidebar";
import MapboxMap from "@/components/MapBox.vue";


export default {
  name: "App",

  components: {
    MapboxMap,
    Sidebar,
  },
};

</script>

<style>
html,
body,
.container {
  height: 100%;
  margin: 0;
}

body {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#app {
  color: #2c3e50;
  height: 100%;
}

</style>