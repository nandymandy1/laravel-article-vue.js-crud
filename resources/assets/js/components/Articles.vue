<template lang="html">
  <div>
    <h2>Articles</h2>
    <form class="mb-3" @submit.prevent="addArticle">
      <div class="form-group">
        <label for="">Title</label>
        <input type="text" placeholder="Title" class="form-control" v-model="article.title">
      </div>
      <div class="form-group mb-2">
        <label for="">Body</label>
        <textarea class="form-control" rows="8" cols="80" placeholder="Article Body" v-model="article.body"></textarea>
      </div>
      <button type="submit" class="btn btn-primary btn-block"><i class="fas fa fa-plus-circle"></i> Add</button>
    </form>
    <nav aria-label="Page navigation example">
        <ul class="pagination">
          <li v-bind:class="[{disabled: !pagination.prev_page_url }]" class="page-item"><a @click="fetchArticles(pagination.prev_page_url)" class="page-link" href="#">Previous</a></li>
          <li class="page-item disabled"><a class="page-link text-dark" href="#">Page {{ pagination.current_page }} of {{ pagination.last_page }}</a></li>
          <li v-bind:class="[{disabled: !pagination.next_page_url }]" class="page-item"><a @click="fetchArticles(pagination.next_page_url)" class="page-link" href="#">Next</a></li>
        </ul>
      </nav>
    <div class="card card-body mb-3" v-for="article in articles" v-bind:key="article.id">
      <h3>{{ article.title }}</h3>
      <p>{{ article.body }}</p>
      <hr>
      <button class="btn btn-md btn-info mb-2" @click="editArticle(article)"><i class="fas fa fa-edit"></i> Edit</button>
      <button class="btn btn-md btn-danger" @click="deleteArticle(article.id)"><i class="fas fa fa-trash"></i> Delete</button>
    </div>
  </div>
</template>




<script>
export default {
  data(){
      return{
        articles: [],
        article:{
          id:'',
          title:'',
          body:''
        },
        article_id: '',
        pagination: {},
        edit: false
      }
  },

  created() {
    this.fetchArticles();
  },
  methods:{
    // fetching all Articles
    fetchArticles(page_url){
      let vm = this;
      page_url = page_url || '/api/articles'
      fetch(page_url)
      .then(res => res.json())
      .then(res => {
        console.log(res.data);
        this.articles = res.data;
        vm.makePagination(res.meta, res.links);
      }).catch(err => console.log(err));
    },
    // Pagination
    makePagination(meta, links){
      let pagination = {
        current_page: meta.current_page,
        last_page: meta.last_page,
        next_page_url: links.next,
        prev_page_url: links.prev
      };
      this.pagination = pagination;
    },
    // Delete Article
    deleteArticle(id){
      if(confirm("Are you sure to delete this Article?")){
        fetch(`api/article/${id}`, {
          method: 'delete'
        })
        .then(res => res.json())
        .then(data => {
          alert('Article Removed');
          this.fetchArticles();
        })
        .catch(err => console.log(err));
      }
    },
    // for add article and update article
    addArticle(){
      if(this.edit === false){
        // Add
        fetch('api/article', {
          method:'post',
          body: JSON.stringify(this.article),
          headers:{
            'content-type': 'application/json'
          }
        }).then(res => res.json())
        .then(data => {
          this.article.title = '';
          this.article.body = '';
          alert('Article Added');
          this.fetchArticles();
        })
        .catch(err => console.log(err))
      }
      else{
        // Update
        fetch('api/article', {
          method:'put',
          body: JSON.stringify(this.article),
          headers:{
            'content-type': 'application/json'
          }
        }).then(res => res.json())
        .then(data => {
          this.article.title = '';
          this.article.body = '';
          alert('Article Updated');
          this.fetchArticles();
        })
        .catch(err => console.log(err))
      }
    },
    editArticle(article){
      this.edit = true,
      this.article.id = article.id,
      this.article.article_id = article.id,
      this.article.title = article.title,
      this.article.body = article.body
    }
  }
}
</script>

<style>

</style>
