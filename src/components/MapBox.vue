<template>
  <div id="map"></div>
</template>

<script>
import mapboxgl from "mapbox-gl";

function Get(yourUrl) {
    var Httpreq = new XMLHttpRequest(); // a new request
    Httpreq.open("GET", yourUrl, false);
    Httpreq.send(null);
    return Httpreq.responseText;
}

function filterOutliers(someArray) {
    if(someArray.length < 4)
    return someArray;

    let values, q1, q3, iqr, maxValue;

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

    return [maxValue, 0];
}

function addLegend(vals, colors){

    var arrayLength = vals.length;
    var layers = ['<'.concat(vals[0].toFixed(2).toString())]
    for (var x = 1; x < arrayLength; x++) {
        layers.push(vals[x-1].toFixed(2).toString().concat(' - ',vals[x].toFixed(2).toString()))
    }
    layers.push('>'.concat(vals[arrayLength-1].toFixed(2).toString()))
    
    document.getElementById("legend").innerHTML = ''
    var i;
    for (i = 0; i < layers.length; i++) {
      var layer = layers[i];
      var color = colors[i];
      var item = document.createElement('div');
      var key = document.createElement('span');
      key.className = 'legend-key';
      key.style.backgroundColor = color;

      var value = document.createElement('span');
      value.innerHTML = layer;
      item.appendChild(key);
      item.appendChild(value);
      document.getElementById("legend").appendChild(item);
    }    
}
        

let json_data = JSON.parse(Get('https://raw.githubusercontent.com/cmaro2/cross-kic/master/JSON/indexesCensal.json'));

let GSIvals = json_data.features.map(f => f.properties.GSIndex);
let GSIs = filterOutliers(GSIvals);
let maxGSI = GSIs[0];
let minGSI = GSIs[1];
let rangeGSI = maxGSI - minGSI;

let GSDvals = json_data.features.map(f => f.properties.GSDensity);
let GSDs = filterOutliers(GSDvals);
let maxGSD = GSDs[0];
let minGSD = GSDs[1];
let rangeGSD = maxGSD - minGSD;

let GSBSvals = json_data.features.map(f => f.properties.GSBSRatio);
let GSBSs = filterOutliers(GSBSvals);
let maxGSBS = GSBSs[0];
let minGSBS = GSBSs[1];
let rangeGSBS = maxGSBS - minGSBS;

let PIvals = json_data.features.map(f => f.properties.prox_avg);
let PIs = filterOutliers(PIvals);
let maxPI = PIs[0];
let minPI = PIs[1];
let rangePI = maxPI - minPI;

let colorsNDVI = ['#d73027', '#fc8d59', '#fee08b', '#ffffbf', '#d9ef8b', '#91cf60', '#1a9850'];
let colorsGSI = ['#ffffe5','#f7fcb9','#d9f0a3','#78c679','#41ab5d','#238443','#005a32'];
let colorsGSD = ['#f9ddda','#f2b9c4','#e597b9','#ce78b3','#ad5fad','#834ba0','#573b88'];
let colorsGSBS = ['#f1eef6','#d0d1e6','#a6bddb','#74a9cf','#3690c0','#0570b0','#034e7b'];
let colorsPI = ['#ffffd4','#fee391','#fec44f','#fe9929','#ec7014','#cc4c02','#8c2d04'];

let valsNDVI = [0.05,0.1,0.2,0.3,0.4,0.5];
let valsGSI= [minGSI+(rangeGSI*0.1), minGSI+(rangeGSI*0.2),minGSI+(rangeGSI*0.4),minGSI+(rangeGSI*0.6),minGSI+(rangeGSI*0.8),maxGSI];
let valsGSD= [minGSD+(rangeGSD*0.1), minGSD+(rangeGSD*0.2),minGSD+(rangeGSD*0.4),minGSD+(rangeGSD*0.6),minGSD+(rangeGSD*0.8),maxGSD];
let valsGSBS= [minGSBS+(rangeGSBS*0.1), minGSBS+(rangeGSBS*0.2),minGSBS+(rangeGSBS*0.4),minGSBS+(rangeGSBS*0.6),minGSBS+(rangeGSBS*0.8),maxGSBS];
let valsPI= [minPI+(rangePI*0.1), minPI+(rangePI*0.2),minPI+(rangePI*0.4),minPI+(rangePI*0.6),minPI+(rangePI*0.8),maxPI];



export default {
  name: "MapboxMap",
  data() {
    // Set initial data, this.createMap() configures event listeners that update data based on user interaction
    return {
      //,
      center: [-3.7, 40.465], // St. Paul
      zoom: 10,
      map: {
        type: Object,
        required: true
      },
      indexLayers: ['SC_NDVI', 'SC_GSI', 'SC_GSD', 'SC_GSBS', 'SC_PI'],
      agrupationLayers: ['SC_barrios', 'SC_distritos'],
      fillAgrupationLayers: ['SC_barrios_fill', 'SC_distritos_fill']
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


      if(layer_id == 'SC_NDVI'){
        addLegend(valsNDVI, colorsNDVI)
        this.$root.$emit('change_layer_map', {'vals': valsNDVI, 'colors':colorsNDVI, 'ind':'SC_NDVI'});
      }
      else if(layer_id == 'SC_GSI'){
        addLegend(valsGSI, colorsGSI)
        this.$root.$emit('change_layer_map', {'vals': valsGSI, 'colors':colorsGSI, 'ind':'SC_GSI'});
      }
      else if(layer_id == 'SC_GSD'){
        addLegend(valsGSD, colorsGSD)
        this.$root.$emit('change_layer_map', {'vals': valsGSD, 'colors':colorsGSD, 'ind':'SC_GSD'});
      }
      else if(layer_id == 'SC_GSBS'){
        addLegend(valsGSBS, colorsGSBS)
        this.$root.$emit('change_layer_map', {'vals': valsGSBS, 'colors':colorsGSBS, 'ind':'SC_GSBS'});
      }
      else if(layer_id == 'SC_PI'){
        addLegend(valsPI, colorsPI)
        this.$root.$emit('change_layer_map', {'vals': valsPI, 'colors':colorsPI, 'ind':'SC_PI' });
      }
    });
    this.$root.$on('change_layer_agrupation', (layer_id) => {

      this.map.setLayoutProperty('SC_barrios', 'visibility', 'none');
      this.map.setLayoutProperty('SC_distritos', 'visibility', 'none');

      if (this.agrupationLayers.includes(layer_id)) {
        this.map.setLayoutProperty(layer_id, 'visibility', 'visible');
      }
    });


  },
  methods: {
    createMap() {
      var vm = this;
      // instantiate map.  this method runs once after the vue component is mounted to the dom
      mapboxgl.accessToken = 'pk.eyJ1IjoiY21hcm8yIiwiYSI6ImNrZ2h1dzBuYTI0bm8yeHFhZHM0NnNzaDYifQ.-PdPlyPaXojGvx_9acr2VA';
      
      let layer = valsNDVI;
      var colors = colorsNDVI;

      addLegend(layer, colors)
      
      vm.map = new mapboxgl.Map({
        container: "map",
        style: 'mapbox://styles/mapbox/light-v10',
        center: this.center,
        zoom: this.zoom
      });
      
      var hoveredStateId = null;

      // set mapbox event listeners to update Vue component data
      vm.map.on('load', function () {
        // Add a source for the state polygons.
        vm.map.addSource('ZonasCensales', {
          'type': 'geojson',
          'data': 'https://raw.githubusercontent.com/cmaro2/cross-kic/master/JSON/indexesCensal.json',
          'generateId': true
        });

        vm.map.addSource('Distritos', {
          'type': 'geojson',
          'data': 'https://raw.githubusercontent.com/cmaro2/cross-kic/master/JSON/indexesDistritos.json'
        })

        vm.map.addSource('Barrios', {
          'type': 'geojson',
          'data': 'https://raw.githubusercontent.com/cmaro2/cross-kic/master/JSON/indexesBarrios.json'
        })


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
                  colorsNDVI[0],
                  valsNDVI[0], colorsNDVI[1],
                  valsNDVI[1], colorsNDVI[2],
                  valsNDVI[2], colorsNDVI[3],
                  valsNDVI[3], colorsNDVI[4],
                  valsNDVI[4], colorsNDVI[5],
                  valsNDVI[5], colorsNDVI[6]],
                  'rgba(255, 255, 255, 0)'],
            'fill-outline-color':
                ['case',
                  ['boolean', ['feature-state', 'hover'], false],
                  'rgba(0, 0, 0, 1)',
                  'rgba(255, 255, 255, 0)'],
            'fill-opacity': 0.6
          }
        }, 'waterway-label');

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
                  colorsGSI[0],
                  valsGSI[0], colorsGSI[1],
                  valsGSI[1], colorsGSI[2],
                  valsGSI[2], colorsGSI[3],
                  valsGSI[3], colorsGSI[4],
                  valsGSI[4], colorsGSI[5],
                  valsGSI[5], colorsGSI[6]],
                  'rgba(255, 255, 255, 0)'],
            'fill-outline-color':
                ['case',
                  ['boolean', ['feature-state', 'hover'], false],
                  'rgba(0, 0, 0, 1)',
                  'rgba(255, 255, 255, 0)'],
            'fill-opacity': 0.6
          }
        }, 'waterway-label');

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
                  colorsGSD[0],
                  valsGSD[0], colorsGSD[1],
                  valsGSD[1], colorsGSD[2],
                  valsGSD[2], colorsGSD[3],
                  valsGSD[3], colorsGSD[4],
                  valsGSD[4], colorsGSD[5],
                  valsGSD[5], colorsGSD[6]],
                  'rgba(255, 255, 255, 0)'],
            'fill-outline-color':
                ['case',
                  ['boolean', ['feature-state', 'hover'], false],
                  'rgba(0, 0, 0, 1)',
                  'rgba(255, 255, 255, 0)'],
            'fill-opacity': 0.6
          }
        }, 'waterway-label');

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
                  colorsGSBS[0],
                  valsGSBS[0], colorsGSBS[1],
                  valsGSBS[1], colorsGSBS[2],
                  valsGSBS[2], colorsGSBS[3],
                  valsGSBS[3], colorsGSBS[4],
                  valsGSBS[4], colorsGSBS[5],
                  valsGSBS[5], colorsGSBS[6]],
                  'rgba(255, 255, 255, 0)'],
            'fill-outline-color':
                ['case',
                  ['boolean', ['feature-state', 'hover'], false],
                  'rgba(0, 0, 0, 1)',
                  'rgba(255, 255, 255, 0)'],
            'fill-opacity': 0.6
          }
        }, 'waterway-label');

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
                  colorsPI[0],
                  valsPI[0], colorsPI[1],
                  valsPI[1], colorsPI[2],
                  valsPI[2], colorsPI[3],
                  valsPI[3], colorsPI[4],
                  valsPI[4], colorsPI[5],
                  valsPI[5], colorsPI[6]],
                  'rgba(255, 255, 255, 0)'],
            'fill-outline-color':
                ['case',
                  ['boolean', ['feature-state', 'hover'], false],
                  'rgba(0, 0, 0, 1)',
                  'rgba(255, 255, 255, 0)'],
            'fill-opacity': 0.6
          }
        }, 'waterway-label');

        vm.map.addLayer({
          'id': 'SC_distritos',
          'type': 'line',
          'source': 'Distritos',
          'layout': {
            'visibility': 'none'
          },
          'paint': {
            'line-opacity': 0.75,
            'line-width': 2
          }
        });
        vm.map.addLayer({
          'id': 'SC_distritos_fill',
          'type': 'fill',
          'source': 'Distritos',
          'layout': {
            'visibility': 'visible'
          },
          'paint': {
            'fill-opacity': 0,
          }
        });

        vm.map.addLayer({
          'id': 'SC_barrios',
          'type': 'line',
          'source': 'Barrios',
          'layout': {
            'visibility': 'none'
          },
          'paint': {
            'line-opacity': 0.75,
            'line-width': 1.25
          }
        });
        vm.map.addLayer({
          'id': 'SC_barrios_fill',
          'type': 'fill',
          'source': 'Barrios',
          'layout': {
            'visibility': 'visible'
          },
          'paint':{
            'fill-opacity': 0
          }
        });
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

          for (var i = 0; i < f.length; i++) {
            if (vm.indexLayers.includes(f[i].layer.id)) {
              hoveredStateId = f[i].id;
              vm.map.setFeatureState(
                  {source: 'ZonasCensales', id: hoveredStateId},
                  {hover: true}
              );
            }
          }
        }
      });

      // location of the click, with description HTML from its properties.
      vm.map.on('click', function (e) {
        let f = vm.map.queryRenderedFeatures(e.point);
        var i;

        for (i = 0; i < f.length; i++) {
          if (vm.fillAgrupationLayers.includes(f[i].layer.id)) {
            if (f[i].layer.id === 'SC_barrios_fill') {
              // eslint-disable-next-line no-unused-vars
              var name_barrio = f[i].properties.Name;
              // eslint-disable-next-line no-unused-vars
              var code_barrio = f[i].properties.GEOCODIGO;
            } else if (f[i].layer.id === 'SC_distritos_fill') {
              // eslint-disable-next-line no-unused-vars
              var name_distrito = f[i].properties.Name;
              // eslint-disable-next-line no-unused-vars
              var code_distrito = f[i].properties.CDIS;
            }
          }
        }

        for (i = 0; i < f.length; i++) {
          if (vm.indexLayers.includes(f[i].layer.id)) {
            var str_index;
            var val_index;
            if (f[i].layer.id === 'SC_NDVI') {
              str_index = 'NDVI';
              val_index = f[i].properties.NDVI;
            } else if (f[i].layer.id === 'SC_GSI') {
              str_index = 'Green Space Index';
              val_index = f[i].properties.GSIndex;
            } else if (f[i].layer.id === 'SC_GSD') {
              str_index = 'Green Space Density';
              val_index = f[i].properties.GSDensity;
            } else if (f[i].layer.id === 'SC_GSBS') {
              str_index = 'Green Space vs Built Space Ratio';
              val_index = f[i].properties.GSBSRatio;
            } else if (f[i].layer.id === 'SC_PI') {
              str_index = 'Proximity Index';
              val_index = f[i].properties.prox_avg;
            }

            new mapboxgl.Popup()
                .setLngLat(e.lngLat)
                .setHTML('<p><strong> District ' + code_distrito + ': </strong>' + name_distrito + '</p>' +
                    '<p><strong> Barrio ' + code_barrio + ': </strong>' + name_barrio + '</p>' +
                    '<strong> CUSEC: ' + f[i].properties.CUSEC + '</strong>' +
                    '<p>The ' + str_index + ' of the area (censal) is: ' + val_index.toFixed(2) + '</p>')
                .addTo(vm.map);
          }
        }
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

 .map-overlay {
  position: absolute;
  bottom: 0;
  right: 0;
  background: rgba(255, 255, 255, 0.8);
  margin-right: 20px;
  font-family: Arial, sans-serif;
  overflow: auto;
  border-radius: 3px;
}

#features {
  top: 0;
  height: 100px;
  margin-top: 20px;
  width: 100px;
}

#legend {
  padding: 10px;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
  line-height: 18px;
  height: 140px;
  margin-bottom: 40px;
  width: 150px;
}

.legend-key {
  display: inline-block;
  border-radius: 20%;
  width: 15px;
  height: 15px;
  margin-right: 5px;
}

</style>