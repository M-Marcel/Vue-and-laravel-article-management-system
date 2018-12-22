<template>
<v-container-fluid>
    <v-toolbar dark class="deep-purple darken-4 navt">  
        <v-toolbar-title> 
            <h3>larticles</h3>    
        </v-toolbar-title> 
      </v-toolbar>  
            <v-layout>
                <v-flex sm6 offset-sm3 class="mt-3"> 
                    <h2>Articles</h2>
                </v-flex>
            </v-layout>
            <v-layout row wrap>
                <v-flex sm6 offset-sm3 class="mt-2">
                    <v-form ref="form" @submit.prevent="addArticle">
                            <v-text-field
                                v-model="article.title"
                                box
                                color="deep-purple"
                                label="Title"
                                type="text"
                                class="pl-3">
                            </v-text-field>
                            <v-textarea
                                v-model="article.body"
                                auto-grow
                                box
                                color="deep-purple"
                                label="Body"
                                rows="2"
                                class="pl-3">
                            </v-textarea>
                            <v-layout justify-center>
                            <v-btn dark large round type="submit" class="deep-purple darken-4 mb-2">Save</v-btn>
                            </v-layout>
                        </v-form>
                </v-flex>
            </v-layout>
            <v-layout justify-center class="mt-3"> 
                    <ul class="pagination">
                        <li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.prev_page_url)">Previous</a></li>
                        <li class="page-item disabled"><a class="page-link text-dark" href="#">{{pagination.current_page}} of {{pagination.last_page}} </a></li>
                        <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item"><a class="page-link" href="#"  @click="fetchArticles(pagination.next_page_url)">Next</a></li>
                    </ul>
                </v-layout>
                <v-layout justify-center>
                    <v-flex xs12 sm8 class="mt-2">
                        <v-card v-for="article in articles" :key ="article.id" class="app-card">
                            <v-layout justify-start>
                                <v-card-title primary-title>
                                    <h3 class="headline pl-3 pr-3">{{ article.title }}</h3>
                                </v-card-title>
                            </v-layout>
                            <v-layout justify-start>
                                <v-card-content primary-title>
                                    <p class="pl-3 pr-3">{{  article.body }}</p>
                                </v-card-content>
                            </v-layout>
                            <v-card-actions>
                                <v-btn dark round @click="editArticle(article)" class="warning">Edit</v-btn>
                                    <v-spacer></v-spacer>
                                <v-btn dark round class="red" @click="deleteArticle(article.id)">Delete</v-btn>
                            </v-card-actions>
                        </v-card>
                    </v-flex>
                </v-layout>         
</v-container-fluid>
</template>

<script>
export default{
    data(){
        return{
            articles:[],
            article:{
                id:'',
                title:'',
                body:''
            },
            article_id:'',
            pagination:{},
            edit:false

        }
    },
    created() {
        this.fetchArticles();
     },
     methods:{
         fetchArticles(page_url){
            let vm = this;
            page_url = page_url || 'api/articles'
            fetch(page_url)  
           .then(res => res.json())
           .then(res => {
                this.articles = res.data ;
               vm.makePagination(res.meta, res.links);
           })
           .catch(err => console.log(err));
         },
         makePagination(meta, links){
         let pagination = {
                current_page: meta.current_page,
                last_page: meta.last_page,
                next_page_url: links.next,
                prev_page_url: links.prev
            }
             this.pagination = pagination;
       },
       deleteArticle(id){
           if(confirm('Are You Sure')){
               fetch(`api/article/${id}`,{
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
       addArticle(){
           if(this.edit == false){
               //add
               fetch('api/article',{
                   method:'post',
                   body:JSON.stringify(this.article),
                   headers:{
                       'content-type':'application/json'
                   }
               })
               .then(res => res.json())
               .then(data => {
                   this.article.title = '';
                   this.article.body = '';
                   alert('Article Added');
                   this.fetchArticles();
               })
               .catch(err => console.log(err));
           }else{
               //update
               fetch(`api/article`,{
                   method:'put',
                   body:JSON.stringify(this.article),
                   headers:{
                       'content-type':'application/json'
                   }
               })
               .then(res => res.json())
               .then(data => {
                   this.article.title = '';
                   this.article.body = '';
                   alert('Article Updated');
                   this.fetchArticles();
               })
               .catch(err => console.log(err));
           
       }
       },
       editArticle(article){
           this.edit = true;
           this.article.id = article.id;
           this.article.article_id = article.id;
           this.article.title = article.title;
           this.article.body = article.body;
       }
    }
};

</script>
<style>
 .navt{
     margin-top: -10px;
 }
 .app-card{
     border: 2px solid #eeeeff;
     padding:10px;
 }

</style>