<template>
  <div class="small">
    <el-card class="box-card">
      <BoxPlotChart :chartData="datacollection" :options="chartOptions"/>
    </el-card>
  </div>
</template>

<script>
import BoxPlotChart from "./BoxPlotChart.js";

export default {
  components: {
    BoxPlotChart
  },
  data() {
    return {
      datacollection: null,
      chartOptions: null
    };
  },
  mounted() {
    this.fillData('01');
    /*
    this.$root.$on('change_selector', (cod_dis) => {
      console.log('recibido', cod_dis)
      this.fillData(cod_dis);

    });

     */
    this.$root.$on('change_selector', (cod_dis) => {
      console.log('recibido en boxplot', cod_dis)
      this.fillData(cod_dis)

    })
  },
  methods: {

    fillData(cod_dis) {
      //Geojson file
      function Get(yourUrl) {
        var Httpreq = new XMLHttpRequest(); // a new request
        Httpreq.open("GET", yourUrl, false);
        Httpreq.send(null);
        return Httpreq.responseText;
      }

      let json_data = JSON.parse(Get('https://raw.githubusercontent.com/cmaro2/cross-kic/master/JSON/indexesCensal.json'));


      //para la etiqueta
      var dict_distritos = {
        1: 'Centro',
        2: 'Arganzuela',
        3: 'Retiro',
        4: 'Salamanca',
        5: 'Chamartín',
        6: 'Tetuán',
        7: 'Chamberi',
        8: 'Fuencarral-El Pardo',
        9: 'Moncloa-Aravaca',
        10: 'Latina',
        11: 'Carabanchel',
        12: 'Usera',
        13: 'Puente de Vallecas',
        14: 'Moratalaz',
        15: 'Ciudad Lineal',
        16: 'Hortaleza',
        17: 'Villaverde',
        18: 'Villa de Vallecas',
        19: 'Vicálvaro',
        20: 'San-Blas Canillejas',
        21: 'Barajas'
      }


      var label_district = dict_distritos[parseInt(cod_dis)]


      //We filter base on the district code obtaining another json object from 'features'
      //Possible to change from filter

      //Future change from filters
      var indicator = 'NDVI'
      //comes from the selector
      var CDIS = cod_dis

      var json_filt_CDIS = json_data.features.filter(function (el) {
        return el.properties.CDIS == CDIS; // Changed this so a home would match
      });

      //var indicators = ['NDVI', 'Green Space Index', 'Green Space Density', 'Green Space/Built Space Ratio', 'Green Space Proximity Index']


      //For data we get the value of the indicator
      var data_f = [json_filt_CDIS.map(function (e) {
        return e.properties[indicator];
      })]
      /*
      var data_f_NDVI = [json_filt_CDIS.map(function (e) {
        return e.properties['NDVI'];
      })]
      var data_f_GSIndex = [json_filt_CDIS.map(function (e) {
        return e.properties["GSIndex"];
      })]
      var data_f_GSDensity = [json_filt_CDIS.map(function (e) {
        return e.properties["GSDensity"];
      })]
      var data_f_GSBS = [json_filt_CDIS.map(function (e) {
        return e.properties['GSBSRatio'];
      })]
      var data_f_prox = [json_filt_CDIS.map(function (e) {
        return e.properties['prox_avg'];
      })]

      console.log([data_f])
      console.log([data_f_NDVI])
      console.log([data_f_GSIndex])
      console.log([data_f_GSDensity])
      console.log([data_f_GSBS])
      console.log([data_f_prox])

       */


      this.chartOptions = {
        responsive: true,
        legend: {
          position: 'top',
        },
        title: {
          display: false,
          text: 'Chart.js Box Plot Chart'
        },
        tooltipDecimals: 3
      };

      this.datacollection = {
        labels: [label_district],
        datasets: [
          {
            label: indicator,
            backgroundColor: "#f87979",
            data: data_f
          },



        ]
      };


    }
  }
  ,


}
;
</script>

<style>
.small {
  height: 300px;
  max-width: 600px;
  /*margin: 150px auto;*/
}
</style>
