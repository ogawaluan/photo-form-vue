<template>
  <div>
    <h2>Verify document</h2>
    <p>{{ isWebcamReady ? 'please, take a photo with your document' : 'We need your permission to take the photo' }}</p>

    <Photo :is-webcam-ready="isWebcamReady" :updatePhoto="(photo) => formData.photo = photo" />    

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
  import Photo from './components/Photo.vue';

  export default {
    data() {
      return {
        formData: {
          name: '',
          photo: null,
        },
        isWebcamReady: false,
      };
    },
    methods: {
      handleSubmit() {
        try {
          console.log('FormData', this.formData);
        } catch(err) {
          console.log('FormError', err);
        }
      },
      updatePhoto(photo) {
        this.formData.photo = photo;
      },
      onChangePermission(permission) {
        this.isWebcamReady = permission
      }
    },
    components: {
      Photo,
    },
  }
</script>
