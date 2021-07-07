<template lang="pug">
div
  video#input-video
  canvas#output-canvas
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import { Camera } from '@mediapipe/camera_utils'

export default defineComponent({
  name: 'App',
  mounted() {
    const video = document.getElementById('input-video')
    const camera = new Camera(video, null)
    camera.start()
    console.log(camera)
  },
})
</script>

<style lang="scss">
video {
  position: absolute;
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;
}
canvas {
  max-width: 100%;
  position: relative;
}
</style>
