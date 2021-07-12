<template lang="pug">
div
  video#input-video
  canvas#output-canvas
</template>

<script>
import { defineComponent } from 'vue'
import 'normalize.css'
import { Camera } from '@mediapipe/camera_utils'
// import drawingUtils from '@mediapipe/drawing_utils'
import { FaceMesh } from '@mediapipe/face_mesh'

export default defineComponent({
  name: 'App',
  mounted() {
    const video = document.getElementById('input-video')
    const camera = new Camera(video, {
      onFrame: () => {
        faceMesh.send({
          image: video,
        })
      },
    })
    const faceMesh = new FaceMesh({
      locateFile: (file) => {
        return `https://cdn.jsdelivr.net/npm/@mediapipe/face_mesh@0.4/${file}`
      },
    })

    camera.start()
    faceMesh.onResults((results) => {
      console.log(results)
    })
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
