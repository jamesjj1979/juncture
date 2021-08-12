<template>
  <div :style="containerStyle">

<div id="jim" v-html="myItem,qt"></div>
please e
  </div>  
</template>

<script>


module.exports = {
  name: 'coords',
  props: { 
    items: { type: Array, default: () => ([]) },
    viewerIsActive: Boolean
   
  },
  data: () => ({
    viewerLabel: 'New Component',
    viewerIcon: 'fas fa-file-code',
    dependencies: []
 
    
    
  }),
  computed: {
    containerStyle() { return { 
      position: 'relative',
      height: this.viewerIsActive ? '100%' : '0',
      myItem() { return this.filteredItems[0] },
      overflowY: 'auto !important' 
    }},
  },
  mounted() { this.loadDependencies(this.dependencies, 0, this.init) },
  methods: {
    init() {
    }
  }
}
console.log(myItem)
</script>

<style>
</style>
