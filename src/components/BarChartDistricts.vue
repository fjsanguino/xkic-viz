<template>
  <el-card class="box-card">
    <canvas id="barchartdistricts"></canvas>
  </el-card>
</template>

<script>
import chart from 'chart.js';

export default {

  mounted() {
    // create the chart after the component is mounted
    //Start with NDVI
    var myBarChart = this.createChart('NDVI')
    //RECIBIR INDICADOR
    this.$root.$on('change_layer', (ind) => {

      var ind_cod_JSON = this.getIndCodJSON(ind)

      //Recalcular en función del cambio de indicador
      var dataLabels = this.getDataLabels(ind_cod_JSON)
      //Actualizar
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
      }
      myBarChart.update()

    });
  },

  methods: {

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


    getDataLabels(ind) {

      function Get(yourUrl) {
        var Httpreq = new XMLHttpRequest(); // a new request
        Httpreq.open("GET", yourUrl, false);
        Httpreq.send(null);
        return Httpreq.responseText;
      }


      var ind_label = ''
      if (ind == 'prox_avg') {
        ind_label = 'Green Space Proximity Index'
      } else if (ind == 'NDVI') {
        ind_label = 'NDVI'
      } else if (ind == 'GSIndex') {
        ind_label = 'Green Space Index'
      } else if (ind == 'GSDensity') {
        ind_label = 'Green Space Density'
      } else if (ind == 'GSBSRatio') {
        ind_label = 'Green Space/Built Space Ratio'
      }


      let json_data_districts = JSON.parse(Get('https://raw.githubusercontent.com/cmaro2/cross-kic/master/JSON/indexesDistritos.json'));

      var json_dist_filt = json_data_districts.features

      var labels_districts = json_dist_filt.map(function (e) {
        return e.properties.Name
      })

      var data_districts = json_dist_filt.map(function (e) {
        return e.properties[ind]
      })


      var returned = [data_districts, labels_districts, ind_label]

      return returned

    },
    createChart(ind) {
      var ctx = document.getElementById('barchartdistricts').getContext('2d');
      var dataLabels = this.getDataLabels(ind)

      var data_districts = dataLabels[0]
      var labels_districts = dataLabels[1]
      var ind_label = dataLabels[2]

      //Call the function to filter the data


      var myBarChart = new chart(ctx, {
        type: 'bar',
        data: {
          labels: labels_districts,
          datasets: [{
            label: ind_label, //titulo,
            data: data_districts,
            fill: true, //
            backgroundColor: '#bbd4e5',
            borderColor: '#7eaed1',
            borderWidth: 1

          }]

        },
        options: {
          title: {
            display: true,
            text: ind_label + ' per District'
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