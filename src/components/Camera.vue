<template>
  <div>
    <video ref="video" width="640" height="480"></video>

    <button 
      @click="capturePhoto"
      :disabled="!permission"
    >Take photo</button>

    <canvas 
      ref="canvas" 
    ></canvas>
  </div>
</template>

<script>
  export default {
    props: ['isWebcamReady', 'updatePhoto'],
    data() {
      return {
        permission: this.isWebcamReady,
      }
    },
    mounted() {
      this.initializeWebcam();
    },
    methods: {
      capturePhoto() {
        const videoRef = this.$refs.video;
        const canvasRef = this.$refs.canvas;
        const ctx = canvasRef.getContext('2d');
        canvasRef.width = 640
        canvasRef.height = 480

        ctx.drawImage(videoRef, 0, 0, canvasRef.width, canvasRef.height);

        canvasRef.toBlob((blob) => {
          const photoFile = new File([blob], 'document.png', { type: 'image/png' });
          this.updatePhoto(photoFile)
        });
      },
      async initializeWebcam() {
        try {
          const stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: false });
          const videoRef = this.$refs.video;
          videoRef.srcObject = stream;
          videoRef.play();
          this.permission = true
          this.$emit('onChangePermission', this.permission);
        } catch (err) {
          console.log('Error to access the webcam', err);
        }
      },
    }
  }
</script>