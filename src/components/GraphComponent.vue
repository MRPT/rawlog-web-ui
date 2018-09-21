<template>
  <div :id="divID">
  </div>
</template>

<script>
import MRPTLIB from '@mrpt/mrpt-web'

export default {
  props: {
    divID: {
      type: String,
      required: true
    },
    plotData: {
      // type: Object,
      required: true
    }
  },
  data () {
    return {
      plot: null
    }
  },
  mounted () {
    let xs
    let ys
    if (!this.plotData) {
      xs = []
      ys = []
    } else {
      xs = this.plotData.xs || []
      ys = this.plotData.ys || []
    }

    this.plot = new MRPTLIB.plots.ScatterPlot(xs, ys, this.divID)
  },
  methods: {
  },
  watch: {
    plotData (newData) {
      console.log('newData', newData)
      const xs = newData.xs || []
      const ys = newData.ys || []
      this.plot.processData(xs, ys)
    }
  }
}
</script>

<style scoped>

</style>
