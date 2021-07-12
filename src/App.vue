<template lang="pug">
div
  video#input-video
  canvas#output-canvas
</template>

<script>
import { defineComponent } from 'vue'
import 'normalize.css'
import { Camera } from '@mediapipe/camera_utils'
import drawingUtils from '@mediapipe/drawing_utils'
import { FaceMesh } from '@mediapipe/face_mesh'
import * as mpFaceMesh from '@mediapipe/face_mesh'

export default defineComponent({
  name: 'App',
  mounted() {
    const video = document.getElementById('input-video')
    const canvas = document.getElementById('output-canvas')
    const canvasCtx = canvas.getContext('2d')
    const camera = new Camera(video, {
      onFrame: () => {
        if (video.videoWidth === 0) return
        const aspect = video.videoHeight / video.videoWidth
        let width = 0
        let height = 0
        if (window.innerWidth > window.innerHeight) {
          height = video.videoHeight
          width = height / aspect
        } else {
          width = video.videoWidth
          height = width * aspect
        }
        canvas.width = width
        canvas.height = height
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
      canvasCtx.save()
      canvasCtx.clearRect(0, 0, canvas.width, canvas.height)
      canvasCtx.drawImage(results.image, 0, 0, canvas.width, canvas.height)
      if (results.multiFaceLandmarks) {
        for (const landmarks of results.multiFaceLandmarks) {
          drawingUtils.drawConnectors(
            canvasCtx,
            landmarks,
            mpFaceMesh.FACEMESH_TESSELATION,
            { color: '#C0C0C070', lineWidth: 1 }
          )
          drawingUtils.drawConnectors(
            canvasCtx,
            landmarks,
            mpFaceMesh.FACEMESH_RIGHT_EYE,
            { color: '#FF3030' }
          )
          drawingUtils.drawConnectors(
            canvasCtx,
            landmarks,
            mpFaceMesh.FACEMESH_RIGHT_EYEBROW,
            { color: '#FF3030' }
          )
          drawingUtils.drawConnectors(
            canvasCtx,
            landmarks,
            mpFaceMesh.FACEMESH_LEFT_EYE,
            { color: '#30FF30' }
          )
          drawingUtils.drawConnectors(
            canvasCtx,
            landmarks,
            mpFaceMesh.FACEMESH_LEFT_EYEBROW,
            { color: '#30FF30' }
          )
          drawingUtils.drawConnectors(
            canvasCtx,
            landmarks,
            mpFaceMesh.FACEMESH_FACE_OVAL,
            { color: '#E0E0E0' }
          )
          drawingUtils.drawConnectors(
            canvasCtx,
            landmarks,
            mpFaceMesh.FACEMESH_LIPS,
            { color: '#E0E0E0' }
          )
        }
      }
      canvasCtx.restore()
    })
  },
})
</script>

<style lang="scss">
video {
  display: none;
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
