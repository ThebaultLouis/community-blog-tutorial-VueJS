<template>
  <div id="">
    <div class="d-flex mt-4 justify-content-between">
      <button @click="getPreviousArticles()" :disabled="articles.prev_page_url === null" class="btn btn-warning">Prev page</button>
      <button @click="getNextArticles()" :disabled="articles.next_page_url === null" class="btn btn-warning">Next page</button></div>
    <div v-if="!loading" class="row">
      <div class="col-md-8 offset-md-2"  v-for="article in articles.data" :key="article.id">
        <Article :article="article"></Article>
      </div>
    </div>
    <div class="loader text-center" v-if="loading">
      <i class="fas fa-7x fa-spin fa-spinner"></i>
    </div>
  </div>
</template>
<script>
import Axios from 'axios';
import config from '@/config';
import Article from '@/components/Article.vue'

export default {
  mounted(){
    this.getArticles();
  },
  components: {
    Article
  },
  data: () => ({
    articles: {},
    submitted: false,
    loading: false
  }),
  methods: {
    getArticles(url = `${config.apiUrl}/articles`){
      this.loading = true
      Axios.get(url).then(response => {
        this.submitted = true;
        this.loading = false;
        this.articles = response.data.data;
      }).catch(response => {
        this.submitted = true
        this.loading = false
        this.$noty.error('Opps Something went wrong!')
        this.console.log(response)
      });
    },
    getNextArticles(){
      this.getArticles(this.articles.next_page_url);
    },
    getPreviousArticles(){
      this.getArticles(this.articles.prev_page_url);
    }
  }
}
</script>
<style lang="css">
.btn-warning {
  color: #ff;
}
</style>
