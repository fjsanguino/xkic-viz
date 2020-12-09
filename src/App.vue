<template>
  <div id="app">
    <el-container class="container">
      <el-aside width="250px">
        <Sidebar/>
      </el-aside>

      <el-container>


        <el-main style="padding-right: 10px;">
          <el-row style="height:600px; margin-bottom: 10px">
            <el-col :span="24">
              <MapboxMap id="map" style="height: auto"/>
              <div class='map-overlay' id="legend"></div>
            </el-col>
          </el-row>

          <el-row style="margin-bottom: 10px">
            <el-col :span="14">

              <BarChartHist style="height: auto"/>
            </el-col>


          </el-row>
          <el-row style="height: 550px">
            <el-col :span="11" style="height: auto">
              <BoxPlot/>
            </el-col>

            <el-col :span="13" style="height: auto">
              <BarChartDistBarr/>
            </el-col>


          </el-row>


          <el-row>

            <el-col>
              <Details/>
            </el-col>
          </el-row>

        </el-main>
      </el-container>
    </el-container>
  </div>
</template>


<script>

import Sidebar from "./components/Sidebar";
import MapboxMap from "@/components/MapBox.vue";
import BoxPlot from "@/components/Boxplot.vue";
import BarChartHist from "@/components/BarChartHistogram";
import Details from "@/components/Details";
import BarChartDistBarr from "@/components/BarChartDistBarr";


export default {
  name: "App",

  components: {
    MapboxMap,
    Sidebar,
    BoxPlot,
    BarChartHist,
    Details,
    BarChartDistBarr
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