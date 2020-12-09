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
    this.fillData('NDVI');

    this.$root.$on('change_layer', (ind) => {
      console.log('recibido en boxplot', ind)

      var ind_cod_JSON = this.getIndCodJSON(ind)
      this.fillData(ind_cod_JSON)


    })
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

    getIndLabel(ind){
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
      //Geojson file
      function Get(yourUrl) {
        var Httpreq = new XMLHttpRequest(); // a new request
        Httpreq.open("GET", yourUrl, false);
        Httpreq.send(null);
        return Httpreq.responseText;
      }

      let json_data = JSON.parse(Get('https://raw.githubusercontent.com/cmaro2/cross-kic/master/JSON/indexesCensal.json'));

      //console.log(json_data.features)
      var indicator = ind

      var arr_districts_names = ['Centro',
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

      var arr_districts_cods = [
          '01' ,
        '02' ,
        '03' ,
        '04' ,
        '05' ,
        '06' ,
        '07' ,
        '08' ,
        '09' ,
        '10' ,
        '11' ,
        '12' ,
        '13' ,
        '14' ,
        '15' ,
        '16' ,
        '17' ,
        '18' ,
        '19' ,
        '20' ,
        '21' ]


      var data_array_districts = []

      for (const cod in arr_districts_cods) {

        var json_filt_cod = json_data.features.filter(function (el) {
          return el.properties.CDIS == arr_districts_cods[cod]; // Changed this so a home would match
        });

        var data_dis = json_filt_cod.map(function (e) {
          return e.properties[indicator];
        })
        data_array_districts.push(data_dis)

      }

      var ind_label = this.getIndLabel(ind)


      this.chartOptions = {
        responsive: true,
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
  ,


}
;
</script>

<style>
.small {
  height: 300px;
  max-width: 800px;
  /*margin: 150px auto;*/
}
</style>
