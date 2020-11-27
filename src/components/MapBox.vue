<template>
  <div id="map"></div>
</template>

<script>
import mapboxgl from "mapbox-gl";

export default {
  name: "MapboxMap",
  data() {
    // Set initial data, this.createMap() configures event listeners that update data based on user interaction
    return {
      center: [-3.7, 40.4], // St. Paul
      zoom: 10,
      map: {
        type: Object,
        required: true
      },
    };
  },
  mounted() {
    // create the map after the component is mounted
    this.createMap();

    this.$root.$on('change_layer', (layer_id) => {

      this.map.setLayoutProperty('SC_NDVI', 'visibility', 'none');
      this.map.setLayoutProperty('SC_GSI', 'visibility', 'none');
      this.map.setLayoutProperty('SC_GSD', 'visibility', 'none');
      this.map.setLayoutProperty('SC_GSBS', 'visibility', 'none');
      this.map.setLayoutProperty('SC_PI', 'visibility', 'none');

      this.map.setLayoutProperty(layer_id, 'visibility', 'visible');
    });


  },
  methods: {
    createMap() {
      var vm = this;
      // instantiate map.  this method runs once after the vue component is mounted to the dom
      mapboxgl.accessToken = 'pk.eyJ1IjoiY21hcm8yIiwiYSI6ImNrZ2h1dzBuYTI0bm8yeHFhZHM0NnNzaDYifQ.-PdPlyPaXojGvx_9acr2VA';

      vm.map = new mapboxgl.Map({
        container: "map",
        style: 'mapbox://styles/mapbox/light-v10',
        center: this.center,
        zoom: this.zoom
      });

      function Get(yourUrl) {
        var Httpreq = new XMLHttpRequest(); // a new request
        Httpreq.open("GET", yourUrl, false);
        Httpreq.send(null);
        return Httpreq.responseText;
      }
      
      function filterOutliers(someArray) {

          if(someArray.length < 4)
            return someArray;

          let values, q1, q3, iqr, maxValue, minValue;

          values = someArray.slice().sort( (a, b) => a - b);//copy array fast and sort

          if((values.length / 4) % 1 === 0){//find quartiles
            q1 = 1/2 * (values[(values.length / 4)] + values[(values.length / 4) + 1]);
            q3 = 1/2 * (values[(values.length * (3 / 4))] + values[(values.length * (3 / 4)) + 1]);
          } else {
            q1 = values[Math.floor(values.length / 4 + 1)];
            q3 = values[Math.ceil(values.length * (3 / 4) + 1)];
          }

          iqr = q3 - q1;
          maxValue = q3 + iqr * 1.5;
          minValue = q1 - iqr * 1.5;

          return [maxValue, minValue];
       }

      var hoveredStateId = null;

      let json_data = JSON.parse(Get('https://raw.githubusercontent.com/cmaro2/cross-kic/master/JSON/indexesCensal.json'));

      // set mapbox event listeners to update Vue component data
      vm.map.on('load', function () {
        // Add a source for the state polygons.
        vm.map.addSource('ZonasCensales', {
          'type': 'geojson',
          'data': 'https://raw.githubusercontent.com/cmaro2/cross-kic/master/JSON/indexesCensal.json',
          'generateId': true
        });

        //let NDVIvals = json_data.features.map(f => f.properties.NDVI);
        //let minNDVI = Math.min(...NDVIvals);
        //let maxNDVI = Math.max(...NDVIvals);
        //let rangeNDVI = maxNDVI-minNDVI;


        vm.map.addLayer({
          'id': 'SC_NDVI',
          'type': 'fill',
          'source': 'ZonasCensales',
          'layout': {
            'visibility': 'visible' //visible, none
          },
          'paint': {
            'fill-color':
                ['case',
                  ['!=', ['get', 'NDVI'], null],
                  ['step', ['get', 'NDVI'], 
                  '#f7fcb9', 
                  0.1, '#d9f0a3',
                  0.2, '#78c679',
                  0.3, '#41ab5d',
                  0.4, '#238443'],
                  'rgba(255, 255, 255, 0)'],
            'fill-outline-color':
                ['case',
                  ['boolean', ['feature-state', 'hover'], false],
                  'rgba(0, 0, 0, 1)',
                  'rgba(255, 255, 255, 0)'],
            'fill-opacity': 0.6
          }
        }, 'waterway-label');

        let GSIvals = json_data.features.map(f => f.properties.GSIndex);
        let GSIs = filterOutliers(GSIvals);
        let maxGSI = GSIs[0];
        let minGSI = GSIs[1];
        let rangeGSI = maxGSI - minGSI;


        vm.map.addLayer({
          'id': 'SC_GSI',
          'type': 'fill',
          'source': 'ZonasCensales',
          'layout': {
            'visibility': 'none'
          },
          'paint': {
            'fill-color':
                ['case',
                  ['!=', ['get', 'GSIndex'], null],
                  ['step', ['get', 'GSIndex'],
                  '#ffffe5', 
                  minGSI, '#f7fcb9', 
                  minGSI+(rangeGSI*0.2), '#d9f0a3',
                  minGSI+(rangeGSI*0.4), '#78c679',
                  minGSI+(rangeGSI*0.6), '#41ab5d',
                  minGSI+(rangeGSI*0.8), '#238443',
                  maxGSI, '#005a32'],
                  'rgba(255, 255, 255, 0)'],
            'fill-outline-color':
                ['case',
                  ['boolean', ['feature-state', 'hover'], false],
                  'rgba(0, 0, 0, 1)',
                  'rgba(255, 255, 255, 0)'],
            'fill-opacity': 0.6
          }
        }, 'waterway-label');

        let GSDvals = json_data.features.map(f => f.properties.GSDensity);
        let GSDs = filterOutliers(GSDvals);
        let maxGSD = GSDs[0];
        let minGSD = GSDs[1];
        let rangeGSD = maxGSD - minGSD;

        vm.map.addLayer({
          'id': 'SC_GSD',
          'type': 'fill',
          'source': 'ZonasCensales',
          'layout': {
            'visibility': 'none'
          },
          'paint': {
            'fill-color':
                ['case',
                  ['!=', ['get', 'GSDensity'], null],
                  ['step', ['get', 'GSDensity'], 
                  '#ffffe5', 
                  minGSD, '#f7fcb9', 
                  minGSD+(rangeGSD*0.2), '#d9f0a3',
                  minGSD+(rangeGSD*0.4), '#78c679',
                  minGSD+(rangeGSD*0.6), '#41ab5d',
                  minGSD+(rangeGSD*0.8), '#238443',
                  maxGSD, '#005a32'],
                  'rgba(255, 255, 255, 0)'],
            'fill-outline-color':
                ['case',
                  ['boolean', ['feature-state', 'hover'], false],
                  'rgba(0, 0, 0, 1)',
                  'rgba(255, 255, 255, 0)'],
            'fill-opacity': 0.6
          }
        }, 'waterway-label');

        let GSBSvals = json_data.features.map(f => f.properties.GSBSRatio);
        let GSBSs = filterOutliers(GSBSvals);
        let maxGSBS = GSBSs[0];
        let minGSBS = GSBSs[1];
        let rangeGSBS = maxGSBS - minGSBS;

        vm.map.addLayer({
          'id': 'SC_GSBS',
          'type': 'fill',
          'source': 'ZonasCensales',
          'layout': {
            'visibility': 'none'
          },
          'paint': {
            'fill-color':
                ['case',
                  ['!=', ['get', 'GSBSRatio'], null],
                  ['step', ['get', 'GSBSRatio'], 
                  '#ffffe5', 
                  minGSBS, '#f7fcb9', 
                  minGSBS+(rangeGSBS*0.2), '#d9f0a3',
                  minGSBS+(rangeGSBS*0.4), '#78c679',
                  minGSBS+(rangeGSBS*0.6), '#41ab5d',
                  minGSBS+(rangeGSBS*0.8), '#238443',
                  maxGSBS, '#005a32'],
                  'rgba(255, 255, 255, 0)'],
            'fill-outline-color':
                ['case',
                  ['boolean', ['feature-state', 'hover'], false],
                  'rgba(0, 0, 0, 1)',
                  'rgba(255, 255, 255, 0)'],
            'fill-opacity': 0.6
          }
        }, 'waterway-label');

        let PIvals = json_data.features.map(f => f.properties.prox_avg);
        let PIs = filterOutliers(PIvals);
        let maxPI = PIs[0];
        let minPI = PIs[1];
        let rangePI = maxPI - minPI;

        vm.map.addLayer({
          'id': 'SC_PI',
          'type': 'fill',
          'source': 'ZonasCensales',
          'layout': {
            'visibility': 'none'
          },
          'paint': {
            'fill-color':
                ['case',
                  ['!=', ['get', 'prox_avg'], null],
                  ['step', ['get', 'prox_avg'], 
                  '#ffffe5', 
                  minPI, '#f7fcb9', 
                  minPI+(rangePI*0.2), '#d9f0a3',
                  minPI+(rangePI*0.4), '#78c679',
                  minPI+(rangePI*0.6), '#41ab5d',
                  minPI+(rangePI*0.8), '#238443',
                  maxPI, '#005a32'],
                  'rgba(255, 255, 255, 0)'],
            'fill-outline-color':
                ['case',
                  ['boolean', ['feature-state', 'hover'], false],
                  'rgba(0, 0, 0, 1)',
                  'rgba(255, 255, 255, 0)'],
            'fill-opacity': 0.6
          }
        }, 'waterway-label');
      });

      this.map.on('mousemove', function (e) {
        let f = vm.map.queryRenderedFeatures(e.point);

        if (f.length > 0) {
          if (hoveredStateId != null) {
            vm.map.setFeatureState(
                {source: 'ZonasCensales', id: hoveredStateId},
                {hover: false}
            );
          }
          hoveredStateId = f[0].id;
          vm.map.setFeatureState(
              {source: 'ZonasCensales', id: hoveredStateId},
              {hover: true}
          );
        }
      });

      // location of the click, with description HTML from its properties.
      vm.map.on('click', function (e) {
        let f = vm.map.queryRenderedFeatures(e.point);
        var str_index;
        var val_index;
        if (f[0].layer.id === 'SC_NDVI') {
          str_index = 'NDVI';
          val_index = f[0].properties.NDVI;
        } else if (f[0].layer.id === 'SC_GSI') {
          str_index = 'Green Space Index';
          val_index = f[0].properties.GSIndex;
        } else if (f[0].layer.id === 'SC_GSD') {
          str_index = 'Green Space Density';
          val_index = f[0].properties.GSDensity;
        }else if (f[0].layer.id === 'SC_GSBS') {
          str_index = 'Green Space vs Built Space Ratio';
          val_index = f[0].properties.GSBSRatio;
        }else if (f[0].layer.id === 'SC_PI') {
          str_index = 'Proximity Index';
          val_index = f[0].properties.prox_avg;
        }

        new mapboxgl.Popup()
            .setLngLat(e.lngLat)
            .setHTML('<strong> CUSEC: ' + f[0].properties.CUSEC + '</strong>' +
                '<p>El ' + str_index + ' de la zona es ' + val_index.toFixed(2) + '</p>')
            .addTo(vm.map);
      });

      // Change the cursor to a pointer when the mouse is over the states layer.
      vm.map.on('mouseenter', 'SC_NDVI', function () {
        vm.map.getCanvas().style.cursor = 'pointer';
      });
      vm.map.on('mouseenter', 'SC_GSI', function () {
        vm.map.getCanvas().style.cursor = 'pointer';
      });
      vm.map.on('mouseenter', 'SC_GSD', function () {
        vm.map.getCanvas().style.cursor = 'pointer';
      });
      vm.map.on('mouseenter', 'SC_GSBS', function () {
        vm.map.getCanvas().style.cursor = 'pointer';
      });
      vm.map.on('mouseenter', 'SC_PI', function () {
        vm.map.getCanvas().style.cursor = 'pointer';
      });

      // Change it back to a pointer when it leaves.
      vm.map.on('mouseleave', 'SC_NDVI', function () {
        vm.map.getCanvas().style.cursor = '';

        if (hoveredStateId) {
          vm.map.setFeatureState(
              {source: 'ZonasCensales', id: hoveredStateId},
              {hover: false}
          );
        }
      });
      // Change it back to a pointer when it leaves.
      vm.map.on('mouseleave', 'SC_GSI', function () {
        vm.map.getCanvas().style.cursor = '';

        if (hoveredStateId) {
          vm.map.setFeatureState(
              {source: 'ZonasCensales', id: hoveredStateId},
              {hover: false}
          );
        }
      });
      vm.map.on('mouseleave', 'SC_GSD', function () {
        vm.map.getCanvas().style.cursor = '';

        if (hoveredStateId) {
          vm.map.setFeatureState(
              {source: 'ZonasCensales', id: hoveredStateId},
              {hover: false}
          );
        }
      });
      vm.map.on('mouseleave', 'SC_GSBS', function () {
        vm.map.getCanvas().style.cursor = '';

        if (hoveredStateId) {
          vm.map.setFeatureState(
              {source: 'ZonasCensales', id: hoveredStateId},
              {hover: false}
          );
        }
      });
      vm.map.on('mouseleave', 'SC_PI', function () {
        vm.map.getCanvas().style.cursor = '';

        if (hoveredStateId) {
          vm.map.setFeatureState(
              {source: 'ZonasCensales', id: hoveredStateId},
              {hover: false}
          );
        }
      });
    }
  },
  // eslint-disable-next-line vue/require-render-return
  render() {
  }
}
</script>

<style>
#map {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
}

.text-container {
  max-width: 500px;
  display: flex;
  flex-direction: column;
  text-align: left;
  align-items: flex-start;
  margin: 0 auto; /* center text container */
}

.mapboxgl-popup {
  max-width: 400px;
  font: 12px/20px 'Helvetica Neue', Arial, Helvetica, sans-serif;
}

#menu {
  position: absolute;
  background: #fff;
  padding: 10px;
  font-family: 'Open Sans', sans-serif;
}

</style>