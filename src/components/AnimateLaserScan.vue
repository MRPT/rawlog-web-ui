<template>
  <div>
    <el-button type="text" @click="dialogVisible = true">{{ text }}</el-button>
    <el-dialog
      title="Animate Laser Scan"
      :visible.sync="dialogVisible"
      width="80%"
      :before-close="handleClose"
      append-to-body="true"
      fullscreen="true"
      close-on-click-modal="false">
      <span>This is component is under development</span>
      <el-container direction="vertical">
        <el-container direcction="horizontal">
          <el-input-number v-model="sleepTime" :min="0" :max="1000000"></el-input-number>
        </el-container>
        <el-container style="padding: 15px;">
          <el-button type="success" @click="handlePlay" :disabled="playDisable">Play</el-button>
        </el-container>
        <el-container style="height:70vh; align-content: center;" id="animation-scene">
          <!-- <div  >
          </div> -->
        </el-container>
      </el-container>
      <span slot="footer" class="dialog-footer">
      </span>
    </el-dialog>
  </div>
</template>

<script>
import MRPTLIB from '@mrpt/mrpt-web'
import { mapGetters } from 'vuex'

export default {
  props: {
    text: String
  },
  data () {
    return {
      dialogVisible: false,
      scene: null,
      append: true,
      arrow: true,
      sleepTime: 100,
      laserScan: null,
      playDisable: false
    }
  },
  methods: {
    handlePlay () {
      console.log('play')
      const ws = this.getWS
      console.log('connected', ws.isConnected)

      const logLoadClient = new MRPTLIB.Service({
        ws,
        name: 'GetRawlogDataFromIndex'
      })

      const arr = this.getTree
      const len = arr.length

      this.play(logLoadClient, ws, len)
    },
    async play (client, ws, len) {
      function sleep (ms) {
        return new Promise(resolve => setTimeout(resolve, ms))
      }

      this.playDisable = true

      for (let i = 0; i < len; i++) {
        const request = new MRPTLIB.ServiceRequest({
          index: i
        })

        const _this = this

        client.callService(request, (result) => {
          if (result.err) {
            console.error('error', result.err)
          }
          if (result && result.laserScan) {
            _this.laserScan.processMessage(Object.assign(result.laserScan, { enable_surface: false }))
          }
        })

        await sleep(this.sleepTime)
      }
      this.playDisable = false
    },
    handleClose (done) {
      this.dialogVisible = false
    },
    initiateScene () {
      if (!document.getElementById('animation-scene')) {
        return
      }
      if (this.scene === null) {
        const parent = document.getElementById('animation-scene')
        const width = parent.getBoundingClientRect().width - 10
        const height = parent.getBoundingClientRect().height - 10
        this.scene = new MRPTLIB.Scene({
          divID: 'animation-scene',
          width,
          height,
          cameraPose: {
            x: -3,
            y: -3,
            z: 5
          }
        })
        this.laserScan = new MRPTLIB.model.CPlanarLaserScan()
        this.scene.addObject(this.laserScan)
        this.scene.controls.handleResize()
      }
    }
  },
  watch: {
    dialogVisible (newValue, oldValue) {
      if (newValue === true) {
        this.initiateScene()
      } else {
        this.scene.controls.handleResize()
      }
    }
  },
  computed: {
    ...mapGetters([
      'getWS',
      'getTree'
    ])
  },
  async mounted () {
    function sleep (ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    }
    this.dialogVisible = true
    await sleep(1)
    this.dialogVisible = false
    await sleep(1)
    this.dialogVisible = true
    await sleep(1)
    this.dialogVisible = false
  }
}
</script>

<style scoped>

</style>
