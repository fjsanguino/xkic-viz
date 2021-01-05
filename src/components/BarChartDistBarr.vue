<template>
  <el-card class="box-card">
    <canvas id="barchart"></canvas>
    <!--
    canvas with the id of the chart (specify in the script part) in the method createChart()
    called when the component is first mounted in the mounted() function
    -->
  </el-card>
</template>

<script>
import chart from "chart.js";
import json_data_districts from '../../assets/data/indexesDistritos.json';

export default {
  mounted() {
    //After the component is mounted, the chart is created (starting with NDVI) and saved in a var called myBarChart for future updates
    var myBarChart = this.createChart('NDVI')

    this.$root.$on('change_layer_agrupation', (layer_id) => {

      console.log(layer_id)
    });

    //Receive the value from change_layer event (indicator) from the Sidebar
    this.$root.$on('change_layer', (ind) => {

      //get the indicator as its in the GEOJSON to filter the data with the dataLabels function
      var ind_cod_JSON = this.getIndCodJSON(ind)
      //Call the functions to get the data and labels for each district based on the indicator coming from the sidebar menu
      var dataLabels = this.getDataLabels(ind_cod_JSON)
      //Update the chart
      myBarChart.data = {
        labels: dataLabels[1],
        datasets: [{
          label: dataLabels[2],
          data: dataLabels[0],

          fill: true, //
          backgroundColor: '#bbd4e5',
          borderColor: '#7eaed1',
          borderWidth: 1
        }]
      };
      myBarChart.options = {
        responsive: true,
        maintainAspectRatio: false,
        title: {
          display: true,
          text: dataLabels[2] + ' value by Districts (calculated for each one)',
          fontSize: 14
        },
        legend: {
          display: false
        }
      }
      myBarChart.update()

    });


  },


  methods: {

    /*
    Function that get the data from the url (geoJSON from github) of the indices calculated for the districts
    using the indicator to filter it
     */
    getDataLabels(ind) {

      /*
      function Get(yourUrl) {
        var Httpreq = new XMLHttpRequest(); // a new request
        Httpreq.open("GET", yourUrl, false);
        Httpreq.send(null);
        return Httpreq.responseText;
      }
      */
      //call the function to get the indicator label cleaned
      var ind_label = this.getIndLabelTitle(ind)

      //let json_data_districts = JSON.parse('../../assets/data/indexesDistritos.json');

      var json_dist_filt = json_data_districts.features

      var labels_districts = json_dist_filt.map(function (e) {
        return e.properties.Name
      })

      var data_districts = json_dist_filt.map(function (e) {
        return e.properties[ind]
      })

      //Return the data array the districts and the label for the indicator name
      var returned = [data_districts, labels_districts, ind_label]

      return returned

    },

    //Base on the indicator name from sidebar component get the label as in the indexes.json
    getIndCodJSON(ind) {
      var ind_cod_JSON = ''

      if (ind == 'SC_NDVI') {
        ind_cod_JSON = 'NDVI'
      } else if (ind == 'SC_GSI') {
        ind_cod_JSON = "GSIndex"
      } else if (ind == 'SC_GSD') {
        ind_cod_JSON = "GSDensity"
      } else if (ind == 'SC_GSBS') {
        ind_cod_JSON = "GSBSRatio"
      } else if (ind == 'SC_PI') {
        ind_cod_JSON = "prox_avg"
      }

      return ind_cod_JSON

    },

    //Get the official name of ind for the title
    getIndLabelTitle(ind) {
      var ind_label = ''
      if (ind == 'prox_avg' || ind == 'SC_PI') {
        ind_label = 'Green Space Proximity Index'
      } else if (ind == 'NDVI' || ind == 'SC_NDVI') {
        ind_label = 'NDVI'
      } else if (ind == 'GSIndex' || ind == 'SC_GSI') {
        ind_label = 'Green Space Index'
      } else if (ind == 'GSDensity' || ind == 'SC_GSD') {
        ind_label = 'Green Space Density'
      } else if (ind == 'GSBSRatio' || ind == 'SC_GSBS') {
        ind_label = 'Green Space/Built Space Ratio'
      }
      return ind_label
    },

    createChart(ind) {

      //Create the chart id
      var ctx = document.getElementById('barchart').getContext('2d');

      //Call the function to obtain the data
      var dataLabels = this.getDataLabels(ind)

      var data_districts = dataLabels[0]
      var labels_districts = dataLabels[1]
      var ind_label = dataLabels[2]

      //create the chart with the corresponding values
      var myBarChart = new chart(ctx, {
        type: 'bar',
        data: {
          labels: labels_districts,
          datasets: [{
            label: 'Count of ' + ind_label, //text for the onhover,
            data: data_districts,
            fill: true, //
            backgroundColor: '#bbd4e5',
            borderColor: '#7eaed1',
            borderWidth: 1
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          legend: {
            display: false
          },
          title: {
            display: true,
            text: ind_label + ' value calculated by Districts',
            fontSize: 14
          }
        }
      })

      myBarChart.update()

      return myBarChart

    }
  }
}
</script>

<style scoped>

</style>