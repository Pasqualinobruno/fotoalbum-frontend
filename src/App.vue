<script>
import axios from 'axios';
import Footer from './components/Footer.vue';

import Header from './components/Header.vue';


export default {
  name: 'App',
  components: {
    Header,
    Footer,

  },
  data() {
    return {
      base_api_url: 'http://127.0.0.1:8000',
      photo_endpoint: '/api/photographys',
      photographys: null
    };
  },
  mounted() {
    const url = this.base_api_url + this.photo_endpoint;
    console.log(url);
    axios
      .get(url).
      then(resp => {
        console.log(resp);
        this.photographys = resp.data.results.data;
      })
      .catch(err => {
        console.error(err);
      })
  }
};

</script>

<template>
  <Header></Header>
  <main>
    <section class="photo" v-if="photographys">
      <div class="container">
        <div class="row">
          <div class="col-12" v-for="photo in photographys" :key="photo.id">
            <div class="card shadow rounded-4">
              <div class="card-body">
                {{ photo.name }}
              </div>
              <div class="card-img-top">
                <section v-if="photo.image.startsWith('https://')">
                  <img :src="photo.image" alt="photo" class="img-fluid">
                </section>
                <section v-else>
                  <img :src="base_api_url + '/storage/' + photo.image" alt="photo" class="img-fluid">
                </section>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
  <Footer></Footer>
</template>

<style></style>
