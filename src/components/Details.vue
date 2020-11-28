<template>
  <div>
    <el-col :span="24">
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <h1><span>Details behind the indicators</span></h1>
        </div>
        <h4>NDVI</h4>
        <div class="text item">
          Data involved:<br>
          Vector layer of NDVI already derived by the City Council. Geo-units are parcel, sidewalk, pedestrian areas and land-use.
          Temporal resolution: 2 seasons/year (spring and autumn)
          <br> <br>

          Computation:<br>
          Zonal statistics using the NDVI from Copernicus as value raster layer and the census units as zone layer.
          <br><br>
          Result: <br>
          Vector layer of census tracts (rows) with the computed statistics in fields (columns).

        </div>
        <h4>Green Space Index</h4>
        <div class="text item">
          Data involved:<br>
          LiDAR classified from the IGN. Use of points classified as mid- and high-vegetation. The vector layer (point
          features) has to be converted to raster. Data provided in 2 km x 2 km and 1 km x 1 km tiles.
          Point could density: >1 pt/m2
          Max. Distance between points = 1.5 m
          Temporal resolution ~ 8 yrs.
          <br> <br>
          Computation:<br>
          spatial query to calculate the area occupied by the green spaces in each census tract, divided by the total
          number of inhabitants.
          <br><br>
          Result: <br>
          vector layer of census tracts (rows) with the computed green space index in fields (columns).
          Units: m2/inhab.
        </div>
        <h4>Green Spaces Proximity Index</h4>
        <div class="text item">
          Data involved:<br>
          Green spaces from the vector cartography 1/1000 provided by the City Council: urban garden, garden area, garden in patio (polygon features).
          Date 2016.
          <br> <br>
          Computation:<br>
          PROX = A / D^2 , where:
          A: area of each patch [m2]
          D: edge-to-edge distance from the patch to the nearest one within a search buffer (radius = 200 m) [m2]
          <br><br>
          Result:<br>
          same green spaces vector layer with a new field with the PROX index.
          Units: dimensionless
          Values: ≥ 0
        </div>

        <h4>Green Space Density</h4>
        <div class="text item">
          Data involved:<br>
          Green spaces from the vector cartography 1/1000 provided by the City Council: urban garden, garden area, garden in patio (polygon features).
          Date 2016.
          <br> <br>
          Computation:<br>
          Spatial query to calculate the area occupied by the green spaces in each census tract divided by the area of the corresponding census tract.
          <br><br>
          Result:<br>
          vector layer of census tracts (rows) with the computed green space density in a new field.
          Units: dimensionless.
          Values: [0 to 1]
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
          Spatial query to calculate the area occupied by the green spaces in each census tract divided by the area occupied by the buildings in the same census tract          <br><br>
          Result:<br>
          vector layer of census tracts (rows) with the computed green-space / built-space ratio in a new field.
          Units: dimensionless.
          Values: ≥ 0 (attention, possible DIV0 error)
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