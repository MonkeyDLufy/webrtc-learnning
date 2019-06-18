<template>
  <div>
    <div style="margin: 20px;">
      <div>使用webrtc实现拍照的demo</div>
      <div>参考链接:
        <a href="https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Taking_still_photos">
          <code>https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Taking_still_photos</code>
        </a>
      </div>
    </div>
    <div class="camera">
      <video id="video">Video stream not available.</video>
      <button id="startbutton">Take photo</button>
    </div>
    <canvas id="canvas" />
    <div class="output">
      <img id="photo" alt="The screen capture will appear in this box.">
    </div>
    
    <div style="margin: 20px;">核心代码</div>
    <div style="margin: 20px;">
      <textarea style="width: 500px; height: 250px;">
        video = document.getElementById('video')
        canvas = document.getElementById('canvas')
        photo = document.getElementById('photo')
        startbutton = document.getElementById('startbutton')
        
        navigator.mediaDevices.getUserMedia({ video: true, audio: false })
          .then(function(stream) {
            video.srcObject = stream
            video.play()
          })
          .catch(function(err) {
            console.log('An error occurred: ' + err)
          })
      </textarea>
    </div>
    
  </div>
</template>

<script>
// We will scale the photo width to this
var width = 320
// This will be computed based on the input stream
var height = 0
var streaming = false
var video = null
var canvas = null
var photo = null
var startbutton = null

export default {
  name: 'Dashboard',
  data() {
    return {}
  },
  mounted() {
    var that = this
    video = document.getElementById('video')
    canvas = document.getElementById('canvas')
    photo = document.getElementById('photo')
    startbutton = document.getElementById('startbutton')

    navigator.mediaDevices.getUserMedia({ video: true, audio: false })
      .then(function(stream) {
        video.srcObject = stream
        video.play()
      })
      .catch(function(err) {
        console.log('An error occurred: ' + err)
      })

    video.addEventListener('canplay', function(ev) {
      if (!streaming) {
        height = video.videoHeight / (video.videoWidth / width)

        video.setAttribute('width', width)
        video.setAttribute('height', height)
        canvas.setAttribute('width', width)
        canvas.setAttribute('height', height)
        streaming = true
      }
    }, false)

    startbutton.addEventListener('click', function(ev) {
      that.takepicture()
      ev.preventDefault()
    }, false)
  },
  methods: {
    takepicture() {
      var context = canvas.getContext('2d')
      if (width && height) {
        canvas.width = width
        canvas.height = height
        context.drawImage(video, 0, 0, width, height)

        var data = canvas.toDataURL('image/png')
        photo.setAttribute('src', data)
      } else {
        this.clearphoto()
      }
    },
    clearphoto() {
      var context = canvas.getContext('2d')
      context.fillStyle = '#AAA'
      context.fillRect(0, 0, canvas.width, canvas.height)

      var data = canvas.toDataURL('image/png')
      photo.setAttribute('src', data)
    }
  }
}
</script>

<style lang="scss" scoped>

a {
  color: #3d7e9a;
  text-decoration: underline;
}

#video {
  border: 1px solid black;
  box-shadow: 2px 2px 3px black;
  width:320px;
  height:240px;
}

#photo {
  border: 1px solid black;
  box-shadow: 2px 2px 3px black;
  width:320px;
  height:240px;
}

#canvas {
  display:none;
}

.camera {
  width: 340px;
  display:inline-block;
  margin: 20px
}

.output {
  width: 340px;
  display:inline-block;
}

#startbutton {
  display:block;
  position:relative;
  margin-left:auto;
  margin-right:auto;
  bottom:32px;
  background-color: rgba(0, 150, 0, 0.5);
  border: 1px solid rgba(255, 255, 255, 0.7);
  box-shadow: 0px 0px 1px 2px rgba(0, 0, 0, 0.2);
  font-size: 14px;
  font-family: "Lucida Grande", "Arial", sans-serif;
  color: rgba(255, 255, 255, 1.0);
}

.contentarea {
  font-size: 16px;
  font-family: "Lucida Grande", "Arial", sans-serif;
  width: 760px;
}
</style>
