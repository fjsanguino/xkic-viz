<template>
  <div>
    <!--
    Use of element ui components (colm and cards to add style for the component of Details behind indicators
    there is no interactivity in this component
     -->

    <el-col :span="24">
      <el-card class="box-card">
        <div slot="header" >
          <h1>Details behind the indicators</h1>
        </div>
        <h4>NDVI</h4>
        <div class="text item">
          Data involved:<br>
          Bands 4 and 8 of Sentinel 2 satellite image.
          Temporal resolution: 16 days

          <br> <br>

          Computation:<br>
          Raster calculation:
          NDVI = (NIR-R) / (NIR+R). Zonal statistics using the NDVI as value raster layer and the census units as zone layer.
          Values: [-1 to 1]



        </div>
        <h4>Green Space Index</h4>
        <div class="text item">
          Data involved:<br>
          Green spaces from the vector cartography 1/1000 provided by the City Council: urban garden, garden area, garden in patio (polygon features).
          Date 2016.
          <br> <br>
          Computation:<br>
          Spatial query to calculate the area occupied by the green spaces in each census tract, divided by the total
          number of inhabitants.

        </div>
        <h4>Green Spaces Proximity Index</h4>
        <div class="text item">
          Data involved:<br>
          Green spaces from the vector cartography 1/1000 provided by the City Council: urban garden, garden area, garden in patio (polygon features).
          Date 2016.
          <br> <br>
          Computation:<br> Average of PROX index of every green space in the census tract
          (PROX = A / D<sup>2</sup> , where
          A: area of each patch [m<sup>2</sup>] and
          D: edge-to-edge distance from the patch to the nearest one within a search buffer (radius = 200 m) [m<sup>2</sup>])
        </div>

        <h4>Green Space Density</h4>
        <div class="text item">
          Data involved:<br>
          Green spaces from the vector cartography 1/1000 provided by the City Council: urban garden, garden area, garden in patio (polygon features).
          Date 2016.
          <br> <br>
          Computation:<br>
          Spatial query to calculate the area occupied by the green spaces in each census tract divided by the area of the corresponding census tract.
        </div>

        <h4>Green-Space Built-Space Ratio</h4>
        <div class="text item">
          Data involved:
          <br>
          Green spaces and buildings from the vector cartography 1/1000 provided by the City Council. Date 2016.
          Green spaces: urban garden, garden area, garden in patio (polygon features).
          Buildings: buildings, building under construction and undefined building.
          <br> <br>
          Computation:<br>
          Spatial query to calculate the area occupied by the green spaces in each census tract divided by the area occupied by the buildings in the same census tract
        </div>
      </el-card>
    </el-col>



  </div>


</template>

<script>

export default {

  name: "Details"
}
</script>

<style scoped>
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}

.clearfix:after {
  clear: both
}

.box-card {
  height: auto;
}
</style>