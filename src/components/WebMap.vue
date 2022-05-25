<template>
  <div id="viewDiv" class="balt-theme"></div>
  
</template>
<script>
  /* eslint-disable */
  import { loadModules } from 'esri-loader';
  export default {
    name: "App",
    mounted() {
      this.loadMap();
    },
    methods: {
      loadMap() {
        loadModules(['esri/Map', 'esri/views/MapView',"esri/layers/GeoJSONLayer"], {
            css: true
          })
          .then(([ArcGISMap, MapView, GeoJSONLayer]) => {
            // create map with the given options
            const map = new ArcGISMap({
              basemap: 'topo-vector'
            });


             const template = {
                title: "Earthquake Info",
                content: "Magnitude {mag} {type} hit {place} on {time}",
                fieldInfos: [
                  {
                    fieldName: 'time',
                    format: {
                      dateFormat: 'short-date-short-time'
                    }
                  }
                ]
              };

              const renderer = {
                type: "simple",
                field: "mag",
                symbol: {
                  type: "simple-marker",
                  color: "orange",
                  outline: {
                    color: "white"
                  }
                },
                visualVariables: [{
                  type: "size",
                  field: "mag",
                  stops: [{
                      value: 2.5,
                      size: "4px"
                    },
                    {
                      value: 8,
                      size: "40px"
                    }
                  ]
                }]
              };

            //  //adding geojson layer
            const geojsonlayer = new GeoJSONLayer({
                url: "https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson",
                // copyright: "USGS Earthquakes"
                popupTemplate: template,
                renderer: renderer,
                orderBy: {
                  field: "mag"
                }
            });
            map.add(geojsonlayer);  // adds the layer to the map
                

            // assign map to this view
            this.view = new MapView({
              container: this.$el,
              map: map,
              center: [-168, 46],
              zoom: 3,
            });
          });
          return()=>{
            if (!this.view){
              this.view.destroy()
             this.view=null
            }
          }
      }

    }
  }
</script>
<style>
  /* esri styles */
  @import url('https://js.arcgis.com/4.4/esri/themes/light/main.css');
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }
  #nav {
    padding: 30px;
  }
  #nav a {
    font-weight: bold;
    color: #2c3e50;
  }
  #nav a.router-link-exact-active {
    color: #42b983;
  }
  #viewDiv {
    position: absolute;
    top: 3.5rem;
    bottom: 0;
    left: 0;
    right: 0;
  }
</style>