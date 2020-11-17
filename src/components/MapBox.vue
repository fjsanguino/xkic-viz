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
      shown_layer: 'SC_NDVI'
    };
  },
  mounted() {
    // create the map after the component is mounted
    this.createMap();

    this.$root.$on('change_layer', () => {

      console.log('UN mensaje muy muy muy muy largo')

      this.map.setLayoutProperty('SC_NDVI', 'visibility', 'none');
      this.map.setLayoutProperty('SC_GSI', 'visibility', 'none');

      if (this.shown_layer === 'SC_NDVI') {
        this.map.setLayoutProperty('SC_GSI', 'visibility', 'visible');
        this.shown_layer = 'SC_GSI';
      } else if (this.shown_layer === 'SC_GSI') {
        this.map.setLayoutProperty('SC_NDVI', 'visibility', 'visible');
        this.shown_layer = 'SC_NDVI';
      }
      console.log(this.shown_layer);
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

      var hoveredStateId = null;

      let json_data = JSON.parse(Get('https://raw.githubusercontent.com/cmaro2/cross-kic/master/indexes.json'));

      // set mapbox event listeners to update Vue component data
      vm.map.on('load', function () {
        // Add a source for the state polygons.
        vm.map.addSource('ZonasCensales', {
          'type': 'geojson',
          'data': 'https://raw.githubusercontent.com/cmaro2/cross-kic/master/indexes.json',
          'generateId': true
        });

        let NDVIvals = json_data.features.map(f => f.properties.NDVI);
        let minNDVI = Math.min(...NDVIvals);
        let maxNDVI = Math.max(...NDVIvals);

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
                  ['interpolate', ['linear'], ['get', 'NDVI'], minNDVI, 'rgba(222,235,247,1)', maxNDVI, 'rgba(49,130,189,1)'],
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
        let minGSI = Math.min(...GSIvals);
        let maxGSI = Math.max(...GSIvals);

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
                  ['interpolate', ['linear'], ['get', 'GSIndex'], minGSI, 'rgba(222,235,247,1)', maxGSI, 'rgba(49,130,189,1)'],
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
      //map.on('foo', function() { alert('bar'); })
      //map.fire('foo');

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