<template>
    <div id="map" :style="containerStyle"></div>

</template>

<script>
const viewerLabel = 'Map Viewer'
const viewerIcon = 'fas fa-map-marker-alt'
const dependencies = [
  'https://cdn.jsdelivr.net/npm/leaflet@1.5.1/dist/leaflet.css',
  'https://cdn.jsdelivr.net/npm/leaflet@1.5.1/dist/leaflet.js',
  "https://github.com/ardhi/Leaflet.MousePosition/blob/master/src/L.Control.MousePosition.js",
  "https://github.com/ardhi/Leaflet.MousePosition/blob/master/src/L.Control.MousePosition.css"

]
// A leaflet baselayer
const baseLayers = {
  'OpenStreetMap': [
    'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
    { maxZoom: 18, attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>' }
  ]
}
const defaults = {
  // Leaflet Map options
  basemap: 'OpenStreetMap',
  center: [25, 0],
  zoom: 2.5,
  maxZoom: 16,
  iconSize: [40, 70]
}
module.exports = {
  name: 've-simple-map',
  props: {
    items: { type: Array, default: () => ([]) },
    allItems: { type: Array, default: () => ([]) },
    entities: { type: Object, default: () => ({}) },
    viewerIsActive: Boolean,
    height: Number
  },
  data: () => ({
    viewerLabel,
    viewerIcon,
    dependencies,
    map: null,
    currentLayers: []
  }),
  computed: {
    mapDef() { return this.items.find(item => item.viewer === this.$options.name) || {} },
    basemap() { return this.mapDef.basemap || defaults.basemap },
    center() { 
      let coordsStr = this.entities[this.mapDef.center] ? this.entities[this.mapDef.center].coords : this.mapDef.center
      return coordsStr ? this.toFloatArray(coordsStr) : defaults.center 
    },
    zoom() { return this.mapDef.zoom || defaults.zoom },
    maxZoom() { return this.mapDef['max-zoom'] || defaults.maxZoom },
    layers() { return this.items.filter(item => item['ve-map-layer']) },
    markers() { return this.items.filter(item => item['ve-map-marker']) },
    mapStyle() { return {
      width: `${this.width}px`,
      height: this.viewerIsActive ? '100%' : '0',
      overflowY: 'auto !important',
      marginLeft: '0' }
    },
    containerStyle() { return {
      width: `${this.width}px`,
      height: this.viewerIsActive ? '100%' : '0' }
    }
  },
  mounted() { this.loadDependencies(this.dependencies, 0, this.init) },
  methods: {
    init() {
      console.log(this.$options.name, this.mapDef)
      if (this.viewerIsActive) this.createMap()
    },
    createMap(reload) {
      if (reload && this.map) {
        this.map.off()
        this.map.remove()
        this.map = null
      }
      if (!this.map) {
        this.$nextTick(() => {
          console.log('createMap', document.getElementById('map').clientHeight)
          this.map = L.map('map', {
            center: this.center, 
            zoom: this.zoom, 
            maxZoom: this.maxZoom, 
            layers: [L.tileLayer(...baseLayers.OpenStreetMap)]
          })
          this.syncLayers()
        })
      }
    },
    toFloatArray(str) { return str.split(',').map(num => parseFloat(num))},
    toIntArray(str) { return str.split(',').map(num => parseInt(num))},
    syncLayers() {
      console.log('syncLayers')
      this.currentLayers.forEach(layer => this.map.removeLayer(layer))
      this.currentLayers = []
      this.layers.forEach(layer => {
        if (layer.heatmap) this.addHeatmap(layer)
      })
      this.markers.forEach(marker => {
        if (marker) this.addCustomMarker(marker)
      })
    },
    
    addCustomMarker(data) {
        console.log('make custom marker', data)
        const faIcon = data.url
        var icon = L.icon({
                iconUrl:      faIcon,
                iconSize:     data.size ? this.toIntArray(data.size) : defaults.iconSize, // size of the icon
                shadowUrl:    data.shadowurl ? data.shadowurl : null,
                shadowSize:   data.shadowsize ? this.toIntArray(data.shadowsize) : defaults.iconSize, // size of the shadow
                iconAnchor:   data.iconanchor ? this.toIntArray(data.iconanchor) : [22, 94], // point of the icon which will correspond to marker's location
                shadowAnchor: data.shadowanchor ? this.toIntArray(data.shadowanchor) : [4, 62],  // the same for the shadow
                popupAnchor:  [-3, -76],
                className:    data.classname ? data.classname : ''
            })
        console.log(icon)
        this.currentLayers.push(L.marker(this.toFloatArray(data.center), {icon}).addTo(this.map))
    },
  },
  watch: {
    active: {
      handler: function () { 
        console.log(`${this.$options.name}.active=${this.viewerIsActive}`) 
        if (this.viewerIsActive && !this.map) this.createMap()
      },
      immediate: false
    },
    mapDef: {
      handler: function (mapDef) {
        console.log('mapDef', mapDef)
        if (this.map) {
          this.syncLayers()
          this.map.flyTo(this.center, this.zoom)
        } else {
          this.createMap()
        }
      },
      immediate: false
    },
    items: {
      handler: function (items) {
        console.log('items', items)
      },
      immediate: true
    },
    height: {
      handler: function (height) {
        if (this.map) this.map.invalidateSize(true)
      },
      immediate: true
    }
  }
}
</script>

<style>
  .map-viewer {
    display: grid;
    grid-template-columns: auto;
    grid-template-rows: 1fr auto;
    grid-template-areas:
    "map-main";
    height: 100%;
  }
  #map {
    grid-area: map-main;
  }
  .map-label {
    /* row-start / column-start / row-end / column-end */
    grid-area: mapcite;
    z-index: 2;
    justify-self: stretch;
    align-self: stretch;
    /* background-color: rgba(255, 255, 255, 0.8); */
    background-color: #ccc;
    /* padding: 3px 6px; */
    text-align: center;
    line-height: 1;
  }
  .title {
    font-size: 0.9rem;
    font-weight: bold;
  }
  .leaflet-container .leaflet-control-mouseposition {
  background-color: rgba(255, 255, 255, 0.7);
  box-shadow: 0 0 5px #bbb;
  padding: 0 5px;
  margin:0;
  color: #333;
  font: 11px/1.5 "Helvetica Neue", Arial, Helvetica, sans-serif;
}
</style>
