<template>
  <div class="hello">
    <input type="text" v-model="new_title">
    <input type="text" v-model="new_content">
    <a @click="add_post()">Dodaj wpis</a>

    <ul>
      <li v-for="post in posts">
        {{post.title}} - 
        {{post.content}}
      </li>
    </ul>
  </div>
</template>

<script>
import db from './firebaseinit'
export default {
  name: 'HelloWorld',
  data () {
    return {
      new_title : '',
      new_content : '',
      posts: [],
    }
  },
  beforeCreate (){
    console.log('before Created');
  },
  beforeMount (){
    console.log('before Mounted');
  },
  mounted (){
    console.log('mounted');
  },
  created(){
    db.collection('posts').get().then((querySnapshot) => {
      querySnapshot.forEach((doc) => {
        console.log(doc.data());

        const data ={
          'title' : doc.data().title,
          'content' : doc.data().content
        }
        this.posts.push(data)
      })
    })
  },
  methods: {
    add_post(){
      db.collection('posts').add({
        title: this.new_title,
        content: this.new_content
      }).then(docRef => {

        db.collection('posts').doc(docRef.id).set({
          post_id : docRef.id,
          title: this.new_title,
          content: this.new_content
        })
        
        console.log(docRef.id)
        const data = {
          title: this.new_title,
          content: this.new_content
        }
        this.posts.push(data)
      }).catch(error => {
        console.log('Ups, zjebałeś', error)
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
