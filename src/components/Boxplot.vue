<template>
    <el-card class="box-card">
      <BoxPlotChart :chartData="datacollection" :options="chartOptions"/>
    </el-card>
</template>

<script>
//import of the javascript code for the boxplot chart that uses 2 specific libraries (one for the boxplot and another to connect chartjs with vue components)
import BoxPlotChart from "./BoxPlotChart.js";
import json_data from '../../assets/data/indexesCensal.json';

export default {
  components: {
    BoxPlotChart
  },
  //data and chartoptions to be sent to the js file
  data() {
    return {
      datacollection: null,
      chartOptions: null
    };
  },
  //After the component is mounted, fillData function is called to create the chart (starting with NDVI)
  mounted() {
    this.fillData('NDVI');

    this.$root.$on('change_layer', (ind) => {

      //when change_layer event happens (from the sidebar the indicator changes)
      //fillData() is called with the ind as its in the JSON files to update the chart
      var ind_cod_JSON = this.getIndCodJSON(ind)
      this.fillData(ind_cod_JSON)


    })
  },
  methods: {
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
    getIndLabel(ind) {
      var ind_label = ''
      if (ind == 'NDVI') {
        ind_label = 'NDVI'
      } else if (ind == 'GSIndex') {
        ind_label = "GS Index"
      } else if (ind == 'GSDensity') {
        ind_label = "GS Density"
      } else if (ind == 'GSBSRatio') {
        ind_label = "GS/Built Space SRatio"
      } else if (ind == 'prox_avg') {
        ind_label = "GS Proximity Index"
      }
      return ind_label
    },

    fillData(ind) {
      //Get the GEOJSON file of the indexes
      /*
      function Get(yourUrl) {
        var Httpreq = new XMLHttpRequest(); // a new request
        Httpreq.open("GET", yourUrl, false);
        Httpreq.send(null);
        return Httpreq.responseText;
      }
      */

      var indicator = ind

      //Creation of 2 arrays (districts_names and codes) to use for the labels and filtering the data
      let arr_districts_names = ['Centro',
        'Arganzuela',
        'Retiro',
        'Salamanca',
        'Chamartín',
        'Tetuán',
        'Chamberi',
        'Fuencarral-El Pardo',
        'Moncloa-Aravaca',
        'Latina',
        'Carabanchel',
        'Usera',
        'Puente de Vallecas',
        'Moratalaz',
        'Ciudad Lineal',
        'Hortaleza',
        'Villaverde',
        'Villa de Vallecas',
        'Vicálvaro',
        'San-Blas Canillejas',
        'Barajas']
      let arr_districts_cods = [
        '01',
        '02',
        '03',
        '04',
        '05',
        '06',
        '07',
        '08',
        '09',
        '10',
        '11',
        '12',
        '13',
        '14',
        '15',
        '16',
        '17',
        '18',
        '19',
        '20',
        '21']

      //Iterate the 21 district codes and to filter the data and push it to a new array (according to each district)
      var data_array_districts = []
      for (const cod in arr_districts_cods) {
        //filter by cod (district) and by indicator
        var json_filt_cod = json_data.features.filter(function (el) {
          return el.properties.CDIS == arr_districts_cods[cod];
        });
        var data_dis = json_filt_cod.map(function (e) {
          return e.properties[indicator];
        })
        data_array_districts.push(data_dis)

      }
      var ind_label = this.getIndLabel(ind)

      //Add the data and options to the chart
      this.chartOptions = {
        responsive: true,
        maintainAspectRatio: false,
        legend: {
          position: 'top',
          display: false
        },
        title: {
          display: true,
          text: ind_label + ' BoxPlot by Districts'
        },
        tooltipDecimals: 3
      };

      this.datacollection = {
        labels: arr_districts_names,
        datasets: [
          {
            label: ind_label,
            backgroundColor: "#f87979",
            data: data_array_districts
          },
        ]
      };
    }
  }
};
</script>

<style>
.small {
  height: auto;
  max-width: 800px;
  /*margin: 150px auto;*/
}
</style>
