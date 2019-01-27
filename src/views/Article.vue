<template>
  <div id="">
    <div class="container my-5">
      <div class="row">
        <div class="col-md-10 offset-md-1">
          <div v-if="!loading" class="card">
            <img heught="420x" :src="article.imageUrl" alt="" class="card-img-top">
            <div class="card-body">
              <h1 class="card-title text-center my-3">{{article.title}}</h1>
              <div class="article-content" v-html="article.content"></div>
            </div>
          </div>
          <div class="loader text-center" v-else>
            <i class="fas fa-7x fa-spin fa-spinner"></i>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Axios from 'axios'
import config from '@/config.js'

export default {
  mounted() {
    this.getArticle();
  },
  data: () => ({
    article: {},
    submitted: false,
    loading: false
  }),
  methods: {
    getArticle(){
      this.loading = true
      Axios.get(`${config.apiUrl}/article/${this.$route.params.id}`).then(response => {
        this.submitted = true;
        this.loading = false;
        this.article = response.data.data;
      }).catch(response => {
        this.submitted = true
        this.loading = false
        this.$noty.error('Opps Something went wrong!')
        this.console.log(response)
      });
    }
  }
}
</script>
<style lang="scss" scoped>
</style>
