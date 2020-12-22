<template>
  <!-- Use of el-container, el-aside, el-main, el-row and el-col from element-ui
  to add style and distribute the components in the page
   -->
  <div id="app">
    <el-container class="container">
      <el-aside width="250px">
        <Sidebar/>
      </el-aside>

      <el-container>

        <el-main style="padding-right: 10px;">
          <!-- Row for the map -->
          <el-row style="height:600px; margin-bottom: 10px">
            <el-col :span="24">
              <MapboxMap id="map" style="height: auto"/>
              <div class='map-overlay' id="legend"></div>
            </el-col>
          </el-row>

          <!-- Row for the Histogram -->
          <el-row style="margin-bottom: 10px; height: 300px; display: flex;">
            <el-col :span="18">
              <BarChartHist style="height: 300px; width: 100%; display: flex;"/>
            </el-col>
          </el-row>

          <!-- Row for the Boxplot and Barchart -->
          <el-row style="margin-bottom: 10px; height: 430px; display: flex;">
            <el-col :span="12">
              <BoxPlot style="height: 100%; width: auto; display: flex;"/>
            </el-col>

            <el-col :span="12">
              <BarChartDistBarr style="height: 100%; width: auto; display: flex;"/>
            </el-col>
          </el-row>

          <!-- Row for the Details behind indicators -->
          <el-row>
            <el-col>
              <Details/>
            </el-col>
          </el-row>

          <el-footer style="margin-top: 8px">

            <div class="logos">
              <p style="font-family: Avenir, Helvetica, Arial, sans-serif;">
                This work has been funded by a cross-KIC activity between EIT Digital and Climate KIC
              </p>

              <a href="https://www.eitdigital.eu/" target="blank">
                <img src="../assets/eitd.png" alt="..." class="img-thumbnail2" style="width: 16%;margin-right: 18px;">
              </a>

              <a href="https://www.climate-kic.org/" target="blank">
                <img src="../assets/EIT-Climate-KIC-EU-flag-transparent.png" alt="..." class="img-thumbnail2" style="width:20%;">
              </a>

              <a href="https://www.upm.es/" target="blank">
                <img src="../assets/UPM_logo.png" alt="..." class="img-thumbnail2" style="width:20%; float:right;">
              </a>

            </div>
          </el-footer>

        </el-main>
      </el-container>
    </el-container>
  </div>
</template>


<script>
//Import the different components created
import Sidebar from "./components/Sidebar";
import MapboxMap from "@/components/MapBox.vue";
import BoxPlot from "@/components/Boxplot.vue";
import BarChartHist from "@/components/BarChartHistogram";
import Details from "@/components/Details";
import BarChartDistBarr from "@/components/BarChartDistBarr";
//add that components to use in the template
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
logos {
  text-align: left;
}
img-thumbnail2 {
  width: 4%;
  height: auto;
}
#barcharthistogram {
  width: 150%;
}
#barchart {
  height: 100%;
  width: 100%;
}
#boxplot {
  width: 80%;
}
.el-card__body {
  width: 95%;
  padding: 20px;
}
</style>