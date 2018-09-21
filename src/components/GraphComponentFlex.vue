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
    traces: {
      // type: Array,
      default: []
    }
  },
  data () {
    return {
      plot: null
    }
  },
  mounted () {
    this.plot = new MRPTLIB.plots.CartesianPlane(this.divID)
    for (let i = 0; i < this.traces.length; i++) {
      this.plot.addTrace(this.traces[i])
    }
  },
  methods: {
  },
  watch: {
    traces: {
      handler (val, oldVal) {
        console.log('traces updated', val)
        this.plot = new MRPTLIB.plots.CartesianPlane(this.divID)
        for (let i = 0; i < this.traces.length; i++) {
          this.plot.addTrace(this.traces[i])
        }
      },
      deep: true
    }
  }
}
</script>

<style scoped>

</style>
