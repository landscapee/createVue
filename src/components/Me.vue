<template>
  <div class="peer">

    <video ref="video" style="height: 100px;height:200px; "></video>

  </div>
</template>
<script>
import Peer from "simple-peer";

export default {
  name: "App",
  data() {

    return {
      socket: null,
      peer1: null,
      peer2: null,
    }
  },
  components: {},
  methods: {

    open() {
      const socket = new WebSocket('wss://localhost:3000');
      this.socket = socket
      socket.addEventListener('open',   (event)=> {

      });
      socket.addEventListener('message', (event) => {

        let stream = JSON.parse(event.data)
        console.log('.....',stream);

        if(stream&&stream.type=="candidate"){
          this.peer1.addStream( stream)
          this.peer1.signal(stream)
        }
      });
      socket.addEventListener('close', function (event) {
        console.log('close ', event);
      });
      socket.addEventListener('error', function (event) {
        console.log('error ', event);
      });

      addEventListener('beforeunload', (d) => {
        socket.close()
      })



    },
    close() {
      if (this.socket) {
        this.socket.close()
      }
    },
    closeMedia() {
      this.peer1.removeStream()
    },

    openMedia(stream){

      this.peer1 = new Peer({initiator: true})
      this.peer2 = new Peer()
      this.peer1.on('signal', data => {
        console.log('peer1   signal');
        this.peer2.signal(data)
        this.socket.send(JSON.stringify(data))
      })
      this.peer2.on('signal', data => {
        console.log('peer2   signal');
        this.peer1.signal(data)
      })
      this.peer2.on('stream', stream => {
        console.log('peer2   stream');
        let video = document.querySelector('video')
        if ('srcObject' in video) {
          video.srcObject = stream
        } else {
          video.src = window.URL.createObjectURL(stream) // for older browsers
        }
        video.play()
      })
    }
  },
  mounted() {
    this.open()
    this.openMedia()
  },
  beforeUnmount() {
    this.peer1.destroy()
    this.peer2.destroy()
  }

}
</script>
<style lang="scss">

</style>
<style lang="scss" scoped>

</style>
