<template>
  <div id="app">
    <el-container class="container">
      <el-aside width="250px">
        <Sidebar/>
      </el-aside>

      <el-container>


        <el-main style="padding-right: 7px;">
          <el-row style="height:800px; margin-bottom: 10px">
            <el-col :span="24">
              <MapboxMap id="map" style="height: auto"/>
            </el-col>
          </el-row>


          <el-row style="height: 520px">
            <el-col :span="14">

              <BarChartDistricts style="height: 400px"/>
            </el-col>


          </el-row>
          <el-row style="height: 520px">
            <el-col :span="10" style="height: 320px">
              <BoxPlot/>
            </el-col>

            <el-col :span="10">
              <RadarChart/>
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
import BarChartDistricts from "@/components/BarChartDistricts";
import Details from "@/components/Details";
import RadarChart from "@/components/RadarChart";


export default {
  name: "App",

  components: {
    MapboxMap,
    Sidebar,
    BoxPlot,
    BarChartDistricts,
    Details,
    RadarChart
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