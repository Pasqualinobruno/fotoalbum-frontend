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
      category_endpoint: '/api/categories',
      photographys: null,
      categories: [],
      search_text: '',
      inEvidence: false,
      selected_category: '',
      pagination: {
        current_page: 1,
        last_page: 1,
        prev_page_url: null,
        next_page_url: null,
      },
    };
  },
  methods: {
    search() {
      let url = `${this.base_api_url}${this.photo_endpoint}?search=${this.search_text}`; //http://127.0.0.1:8000/api/photographys?search= testo cercato nel search
      if (this.inEvidence) {
        url += `&inEvidence=true`; //+=&inEvidence=true
      }
      if (this.selected_category) {
        url += `&category=${this.selected_category}`; //&category= piu la categoria selezionata
      }
      console.log(url);
      this.callApi(url);
    },
    callApi(url) {
      axios
        .get(url)
        .then(resp => {
          console.log(resp);
          this.photographys = resp.data.results.data; //dati photo
          this.pagination = resp.data.results; //dati paginazione
        })
        .catch(err => {
          console.error(err);
        });
    },
    fetchCategories() {
      axios
        .get(`${this.base_api_url}${this.category_endpoint}`)
        .then(resp => {
          this.categories = resp.data;
        })
        .catch(err => {
          console.error(err);
        });
    },
    go(url) {
      console.log(url);
      this.callApi(url);
    }
  },
  mounted() {
    const url = `${this.base_api_url}${this.photo_endpoint}`; //http://127.0.0.1:8000/api/photographys
    console.log(url);
    this.callApi(url);
    this.fetchCategories();
  }
};
</script>

<template>
  <Header></Header>
  <main>
    <section class="photo" v-if="photographys !== null">
      <div class="container">
        <form @submit.prevent="search()">
          <div class="input-group mb-3">
            <select class="form-select" v-model="selected_category">
              <option value="">Tutte le categorie</option>
              <option v-for="category in categories.results" :key="category.id" :value="category.id">
                {{ category.name }}
              </option>
            </select>
          </div>
          <div class="input-group mb-3">
            <div class="form-check form-check-inline ml-3">
              <input class="form-check-input" type="checkbox" id="inEvidence" v-model="inEvidence">
              <label class="form-check-label" for="inEvidence">In evidenza</label>
            </div>
            <input type="search" class="form-control" placeholder="search..." v-model="search_text">
            <button class="btn btn-outline-dark" type="submit">
              <i class="fas fa-search fa-lg fa-fw"></i>
            </button>
          </div>
        </form>
        <div class="row">
          <section v-if="photographys === null">
            <div class="col-12">
              <h2>Caricamento in corso...</h2>
            </div>
          </section>
          <section v-else-if="photographys.length === 0">
            <div class="col-12">
              <h2>Non ci sono risultati</h2>
            </div>
          </section>
          <section v-else>
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
          </section>
        </div>
      </div>
    </section>
    <section v-else>
      <div class="container">
        <div class="col-12">
          <h2>Non ci sono risultati</h2>
        </div>
      </div>
    </section>
    <div class="container">
      <nav aria-label="Page navigation" class="mt-4 col-1" v-if="pagination && pagination.last_page > 1">
        <ul class="pagination">
          <li class="page-item" :class="{ 'disabled': !pagination.prev_page_url }">
            <button class="page-link" type="button" @click="go(pagination.prev_page_url)" aria-label="Previous">
              <span aria-hidden="true">&laquo;</span>
            </button>
          </li>
          <li v-for="page in pagination.last_page" :key="page" class="page-item"
            :class="{ 'active': page === pagination.current_page }">
            <button class="page-link" type="button"
              @click="go(`${base_api_url}${photo_endpoint}?page=${page}&search=${search_text}${inEvidence ? '&inEvidence=true' : ''}`)">{{
      page }}</button>
          </li>
          <li class="page-item" :class="{ 'disabled': !pagination.next_page_url }">
            <button class="page-link" type="button" @click="go(pagination.next_page_url)" aria-label="Next">
              <span aria-hidden="true">&raquo;</span>
            </button>
          </li>
        </ul>
      </nav>
    </div>
  </main>
  <Footer></Footer>
</template>
