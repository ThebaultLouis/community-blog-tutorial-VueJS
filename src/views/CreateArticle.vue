<template>
  <div id="">
    <div class="row">
      <div class="col-md-8 offset-md-2">
        <div class="card my-5">
          <div class="card-body">

            <picture-input
              ref="pictureInput"
              width="600"
              height="600"
              accept="image/jpeg,image/png"
              size="10"
              button-class="btn btn-danger"
              @change="onChange">
            </picture-input>

            <select v-model="category" name="" id="" class="form-control my-3">
              <option selected value="">Select a category</option>
              <option v-for="category in categories" :key="category.id" :value="category.id">{{category.name}}</option>
            </select>

            <input v-model="title" placeholder="Title" type="text" class="form-control mb-3">

            <wysiwyg v-model="content" />

            <div class="text-center mt-3">
              <button :disabled="loading" @click="CreateArticle()" class="btn btn-success">
                <i v-if="loading" class="fas fa-spin fa-spinner"></i>
                {{loading ? '': 'Create an article'}}
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import PictureInput from 'vue-picture-input';
import Axios from 'axios';
import Config from '@/config.js';

export default {
  beforeRouteEnter(to, from, next) {
    if (!localStorage.getItem('auth')){
      return next({path: '/'});
    }
    next();
  },
  mounted(){
    this.getCategories();
  },
  data: () => ({
    title: "",
    content: "",
    image: null,
    categories: [],
    category: "",
    loading: false
  }),
  components: {
    PictureInput
  },
  methods: {
    onChange(image){
      this.image = image;
    },
    CreateArticle(){
      if (!this.title || !this.image || !this.category || !this.content){
        this.$noty.error("Please fill in all fields.");
        return;
      }
      this.loading = true

      const form = new FormData();
      form.append('file', this.image);

      form.append('upload_preset', process.env.VUE_APP_CLOUDINARY_PRESET);
      form.append('api_key', process.env.VUE_APP_CLOUDINARY_API_KEY);

      Axios.post(process.env.VUE_APP_CLOUDINARY_URL, form)
      .then(res => Axios.post(`${Config.apiUrl}/articles`, {
        title: this.title,
        content: this.content,
        category_id: this.category,
        imageUrl: res.data.secure_url
      }, {
        headers: {
          Authorization: `Bearer ${this.$root.auth.token}`
        }
      }).then(() => {
        this.loading = false;
        this.$noty.success('Article created successfully.');
        this.$router.push('/');
      }).catch(()=> {
        this.loading = false;
        this.$noty.error('Oops ! Something went wrong.');
      }))
    },
    getCategories(){
      const categories = localStorage.getItem('categories');
      if (categories){
        this.categories = JSON.parse(categories);
        return;
      }
      Axios.get(`${Config.apiUrl}/categories`)
      .then(response => {
        this.categories = response.data.categories;
        localStorage.setItem("categories", JSON.stringify(this.categories));
      })
    }
  }
}
</script>
<style lang="css" scoped>
</style>
