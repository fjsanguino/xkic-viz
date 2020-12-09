<template>
  <el-card class="box-card">
    <canvas id="barcharthistogram"></canvas>
  </el-card>
</template>

<script>
import chart from 'chart.js';

let colorsNDVI = ['#d73027', '#fc8d59', '#fee08b', '#ffffbf', '#d9ef8b', '#91cf60', '#1a9850'];
let valsNDVI = [0.05, 0.1, 0.2, 0.3, 0.4, 0.5];

export default {

  mounted() {
    // create the chart after the component is mounted
    //Start with NDVI
    var myBarChart = this.createChart('NDVI')

    //RECIBIR INDICADOR
    this.$root.$on('change_layer_map', (received) => {
      var intervals = received['vals']
      var colors = received['colors']
      var ind = received['ind']

      //Return of the function count_intervals, colors, intervals and the ind_lbel
      var result_hist = this.calculateHistogramData(intervals, ind)

      var data_hist = result_hist[0]
      var ind_label = result_hist[1]
      var labels_int = this.buildIntervalsLabels(intervals)

      myBarChart.data = {
        labels: labels_int,
        datasets: [{
          label: 'Count of ' + ind_label, //titulo,
          data: data_hist,
          fill: true, //
          backgroundColor: colors,
          borderColor: colors,
          borderWidth: 1

        }]
      };
      myBarChart.options =  {
        legend:{
          display: false
        },
        title: {
          display: true,
              text: ind_label + ' Histogram (ZCs)',
              fontSize: 14
        }
      }
      myBarChart.update()

    });



  },

  methods: {


    calculateHistogramData(intervals, ind) {

      //Get the data from indexes censal and filter the data by the indicator
      function Get(yourUrl) {
        var Httpreq = new XMLHttpRequest(); // a new request
        Httpreq.open("GET", yourUrl, false);
        Httpreq.send(null);
        return Httpreq.responseText;
      }

      let json_data_censal = JSON.parse(Get('https://raw.githubusercontent.com/cmaro2/cross-kic/master/JSON/indexesCensal.json'));
      var indicator = this.getIndCodJSON(ind);
      var ind_label = this.getIndLabelTitle(ind);

      var json_cens_filt = json_data_censal.features
      var data_censal = json_cens_filt.map(function (e) {
        return e.properties[indicator]
      })


      //Function that calculates the count for each interval passed
      var counts_intervals = [0, 0, 0, 0, 0, 0, 0]
      for (const value of data_censal) {

        if (value < intervals[0]) {
          ++counts_intervals[0]
        } else if (intervals[0] < value && value < intervals[1]) {
          ++counts_intervals[1]
        } else if (intervals[1] < value && value < intervals[2]) {
          ++counts_intervals[2]
        } else if (intervals[2] < value && value < intervals[3]) {
          ++counts_intervals[3]
        } else if (intervals[3] < value && value < intervals[4]) {
          ++counts_intervals[4]
        } else if (intervals[4] < value && value < intervals[5]) {
          ++counts_intervals[5]
        } else if (intervals[5] < value) {
          ++counts_intervals[6]
        }
      }

      //Return of the function count_intervals, colors, intervals and the ind_lbel
      return [counts_intervals, ind_label]
    },

    //Base on the indicator name from mapbox component get the label as in the indexes.json
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

    // Get the data and labels for Districts
    getDataLabels(ind) {

      function Get(yourUrl) {
        var Httpreq = new XMLHttpRequest(); // a new request
        Httpreq.open("GET", yourUrl, false);
        Httpreq.send(null);
        return Httpreq.responseText;
      }

      //call the function to get the indicator label cleaned
      var ind_label = this.getIndLabelTitle(ind)


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

    buildIntervalsLabels(intervals) {

      var labelsIntervals = []

      labelsIntervals.push('< ' + intervals[0].toFixed(2))
      labelsIntervals.push(intervals[0].toFixed(2) + '-' + intervals[1].toFixed(2))
      labelsIntervals.push(intervals[1].toFixed(2) + '-' + intervals[2].toFixed(2))
      labelsIntervals.push(intervals[2].toFixed(2) + '-' + intervals[3].toFixed(2))
      labelsIntervals.push(intervals[3].toFixed(2) + '-' + intervals[4].toFixed(2))
      labelsIntervals.push(intervals[4].toFixed(2) + '-' + intervals[5].toFixed(2))
      labelsIntervals.push('> ' + intervals[5].toFixed(2))

      return labelsIntervals

    },

    createChart(ind) {

      console.log(ind)
      var result_hist = this.calculateHistogramData(valsNDVI, 'SC_NDVI')
      var data = result_hist[0]
      var ind_label_hist = result_hist[1]
      //Return of the function count_intervals, colors, intervals and the ind_lbel
      var ctx = document.getElementById('barcharthistogram').getContext('2d');

      /*
      var dataLabels = this.getDataLabels(ind)

      var data_districts = dataLabels[0]
      var labels_districts = dataLabels[1]
      var ind_label = dataLabels[2]

      console.log('data_districts', data_districts)
      console.log('labels_districts', labels_districts)
      console.log('ind_label', ind_label)

       */

      //Call the function to filter the data


      var labels = this.buildIntervalsLabels(valsNDVI)

      var myBarChart = new chart(ctx, {
        type: 'bar',
        data: {
          labels: labels,
          datasets: [{
            label: 'Count of ' + ind_label_hist, //titulo,
            data: data,
            fill: true, //
            backgroundColor: colorsNDVI,
            borderColor: colorsNDVI,
            borderWidth: 1
          }]
        },
        options: {
          legend:{
            display: false
          } ,
          title: {
            display: true,
            text: ind_label_hist + ' Histogram (ZCs)',
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