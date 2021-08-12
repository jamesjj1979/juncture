<template>
  <div :style="containerStyle">
  <div v-html="myItem" id="quot"></div>
  testing 15
  </div>  
</template>

<script>
module.exports = {
  name: 'quotes',
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
  filteredItems() { return this.items.filter(item => item[this.componentName]) },
        myItem() { return this.filteredItems[0]["qt"] },
  containerStyle() { return { 
      position: 'relative',
      height: this.viewerIsActive ? '100%' : '0', 
      overflowY: 'auto !important' 
    }},
  },
  mounted() { this.loadDependencies(this.dependencies, 0, this.init) },
  methods: {
    init() {
    }
  }
}


</script>

<style>
#quot{
font-size: 25px;
font-family: "Lucida handwriting";
color:red;
}
</style>
