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
        sendCount: 1,
        intervalId: null
      }
    },
    mounted () {
      this.video = this.$refs.video
      navigator.mediaDevices.getUserMedia({ video: true }).then(mediaStream => {
        this.video.srcObject = mediaStream
      })
      .catch(error => error)
      this.canvas = this.$refs.canvas
      const username = this.$store.getters.user.username
      const contestID = this.$route.params.contestID
      const totalSendCount = 5
      const delay = 60000
      let intervalId = setInterval(() => {
        this.captureAndSend(username, contestID)
        if (this.sendCount === totalSendCount) {
          clearInterval(intervalId)
        }
        this.sendCount++
      }, delay)
      this.intervalId = intervalId
    },
    methods: {
      captureAndSend (currentUsername, currentContestID) {
        this.canvas.getContext('2d').drawImage(this.video, 0, 0, 320, 240)
        const image = this.canvas.toDataURL('image/webp')
        api.sendProctorImage({
          username: currentUsername,
          contestID: currentContestID,
          dataURL: image
        })
      }
    },
    beforeDestroy () {
      clearInterval(this.intervalId)
      this.video.srcObject.getTracks().forEach((track) => {
        track.stop()
      })
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