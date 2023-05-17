<template>
  <div>
    <h2>Verify document</h2>
    <p>{{ isWebcamReady ? 'please, take a photo with your document' : 'We need your permission to take the photo' }}</p>

    <video ref="video" width="640" height="480"></video>

    <button 
      @click="capturePhoto"
      :disabled="!isWebcamReady"
    >Take photo</button>

    <canvas 
      ref="canvas" 
    ></canvas>

    <form 
      @submit.prevent="handleSubmit" 
      enctype="multipart/form-data"
    >
      <label for="name">Please, provide your name</label>
      <input type="text" id="name" v-model="formData.name" required>

      <input type="hidden" name="photo" :value="formData.photo">

      <button type="submit" :disabled="!formData.photo">Submit document photo</button>
    </form>
  </div>  
</template>

<script>
  export default {
    data() {
      return {
        isWebcamReady: false,
        formData: {
          name: '',
          photo: null,
        }
      };
    },
    mounted() {
      this.initializeWebcam();
    },
    methods: {
      async initializeWebcam() {
        try {
          const stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: false });
          const videoRef = this.$refs.video;
          videoRef.srcObject = stream;
          videoRef.play();
          this.isWebcamReady = true;
        } catch (err) {
          console.log('Error to access the webcam', err);
        }
      },
      capturePhoto() {
        const videoRef = this.$refs.video;
        const canvasRef = this.$refs.canvas;
        const ctx = canvasRef.getContext('2d');
        canvasRef.width = 640
        canvasRef.height = 480

        ctx.drawImage(videoRef, 0, 0, canvasRef.width, canvasRef.height);

        canvasRef.toBlob((blob) => {
          this.formData.photo = new File([blob], 'document.png', { type: 'image/png' });
        });

      },
      handleSubmit() {
        try {
          console.log('FormData', this.formData);
        } catch(err) {
          console.log('FormError', err);
        }
      }
    }
  }
</script>
