<template>
  <div :style="containerStyle">
<div id="j">working still</div>
  Test
  </div>  
</template>

<script>
module.exports = {
  name: 'quotes',
  props: { 
    items: { type: Array, default: () => ([]) },
    viewerIsActive: Boolean
    console.log(items)
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
</style>
