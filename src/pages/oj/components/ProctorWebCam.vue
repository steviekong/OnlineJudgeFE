<template>
  <div id = "comp">
    <div>
      <video ref="video" id="video" width="320" height="240" autoplay></video>
      <canvas ref="canvas" id="canvas" width="320" height="240"></canvas>
    </div>
  </div>  
</template>
<script>
  import api from '@oj/api'

  export default {
    name: 'ProctorWebCam',
    data () {
      return {
        video: {},
        canvas: {},
        sendCount: 1
      }
    },
    mounted () {
      this.video = this.$refs.video
      if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
        navigator.mediaDevices.getUserMedia({ video: true }).then(mediaStream => {
          this.video.srcObject = mediaStream
        })
        .catch(error => console.error('getUserMedia() error:', error))
      }
      this.canvas = this.$refs.canvas
      const username = this.$store.getters.user.username
      const problemId = this.$route.params.problemId
      const totalSendCount = 15
      const delay = 60000
      let intervalId = setInterval(() => {
        this.captureAndSend(username, problemId)
        if (this.sendCount === totalSendCount) {
          clearInterval(intervalId)
        }
        this.sendCount++
      }, delay)
    },
    methods: {
      captureAndSend (username, problemId) {
        this.canvas.getContext('2d').drawImage(this.video, 0, 0, 320, 240)
        const image = this.canvas.toDataURL('image/webp')
        // Implement send here
        console.log('sent')
      }
    }
  }
</script>

<style>
    body: {
        background-color: #F0F0F0;
    }
    #comp {
        text-align: center;
        color: #2c3e50;
    }
    #video {
        background-color: #000000;
    }
    #canvas {
        display: none;
    }
</style>