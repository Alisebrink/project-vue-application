
  <!-- Den här appen är skapad av Linnea Isebrink som projektarbete för kursen DT162G - JavaScriptbaserad webbutveckling -->
  <template>
  <div class="container">
    <h1>Min att-göra-lista</h1>
    <div class="create-post">
      <!-- Skapa post här -->
      <!-- Här är ett input fält som tillåter att vi lägger till en post på listan -->
      <input type="text" id="create-post" v-model="text">
      <button class="add-task-button" v-on:click="createPost">Lägg till en uppgift</button>
    </div>
    <!-- Om innehållet inte kan läsas ut så dyker det upp ett error-meddelande här -->
    <p class="error" v-if="error">{{error}}</p>
    <div class="posts-container">
      <div class="post"
        v-for="(post, index) in posts"
        v-bind:item="post"
        v-bind:index="index"
        v-bind:key="post._id"
      >
      <!-- Här är min checkbox som man kryssar i när uppgiften på listan är färdig -->
      <input type="checkbox" class="finish-task"
      v-model="post.isFinished"
      >
      <!-- Det här är p-taggen som innehåller texten i en post -->
      <p class="text" title="Uppdatera mig"
        v-if ="!post.editmode"
        v-on:click="enterEditmode(post)"
        v-bind:class="{finishedTask: post.isFinished}"
        >
        {{post.text}}
      </p>
      <!-- Här är input-fältet som dyker upp när man klickar på p-taggen ovan -->
      <input class="input-field"
        v-else
        v-model="post.text"
        >
        <!-- Den här lägger till datumet då posten skapades -->
        <p class="text-date">Tillagd {{`${post.createdAt.getDate()}/${post.createdAt.getMonth()}/${post.createdAt.getFullYear()}`}}</p>

      <!-- Knappen som låter mig uppdatera posten -->
      <!-- Knappen fungerar bara så länge som checkboxen inte är ikryssad-->
      <button class="update-button"
        v-bind:class="{inactiveButton: post.isFinished}"
        v-on:click="updatePost(post._id, post.text)" 
        :disabled="post.isFinished">Uppdatera</button>

      <!-- Min knapp som raderar den post du trycker på -->
      <button class="delete-button"
        v-on:click="deletePost(post._id)">Radera</button>

      
    </div>
  </div>
  </div>
</template>

<script>
import PostService from '../postService'; // importerar kod från min fil som heter postService

export default {
  name: 'postComponent',
  data() {
    return {
      posts: [],
      error: '',
      text: '',
      isFinished:true, // den här används för min checkbox
    } 
  },
  async created() { // den här funktionen läser in mina poster
    try {
      this.posts = await PostService.getPosts();
    } catch(err) {
      this.error = err.message;
    }
  },
  // Här nedan finns mina metoder som används för att genomföra olika saker på front-end sidan
  methods: {
    async createPost() { // Skapar upp en post, den funkar bara så länge som fältet inte är tomt
      if (this.text != ""){
        await PostService.insertPost(this.text);
        this.posts = await PostService.getPosts();
      }
    },
    async deletePost(id) { // raderar en post
      await PostService.deletePost(id);
      this.posts = await PostService.getPosts();
    },
    async updatePost(id, text) { // uppdaterar texten på en post
      await PostService.updatePost(id, text);
      this.posts = await PostService.getPosts();
    },
    enterEditmode(post) { // Det här är en egen funktion som aktiveras när man klickar på en p-tagg i min lista, då dyker det upp ett input-fält
      if (!post.isFinished){
        post.editmode = true;
        this.posts = [...this.posts];
      }
    },
  },

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

div.container {
  max-width: 800px; 
  margin: 0 auto;
  background:#F8F8F8;
}

p.error { 
  border: 1px solid #ff5b5f; 
  background-color: #ffc5c1; 
  padding: 10px; 
  margin-bottom: 15px; 
}

.delete-button, .update-button, .finish-task, .add-task-button {
  padding:5px 15px;
  margin:5px;
  font-size: 10px;
  font-weight:700;
  letter-spacing: 1px;
  text-transform:uppercase;
  color:white;
  background:#027A9F;
  border:none;
  border-radius: 5px;
  height:40px;
  width:auto;
}

.delete-button:hover, .update-button:hover, .finish-task:hover, .add-task-button:hover {
  color:black;
  background:#FFAA01;
  cursor:pointer;
  transition:0.2s;
}

div h1 {
  background:#027A9F;
  color:white;
  padding-top:40px;
  padding-bottom:40px;
}

div.post:hover {
  border:1px solid #adadad;
  transition:0.2s;
}

div.post {
  border:1px solid white;
  position: relative; 
  background-color: white;
  padding:10px;
  margin: 20px 15px;
  display:flex;
  border-radius: 5px;
}

div.posts-container {
  box-sizing: border-box;
  padding:5px;
}

div.created-at {  
  background-color: darkgreen; 
}

p.text {
  font-size: 16px; 
  font-weight: 700;
  width:60%;
  text-align: left;
  margin-left:10px;
}

.input-field {
  width:70%;
  height:20px;
  margin-top:10px;
  margin-bottom:10px;
  margin-right:15px;
  padding:5px 15px;
  border-radius:5px;
  border:1px solid #FFAA01;
}

p.text-date {
  font-size:12px;
  text-align: center;
  color:#adadad;
  margin-right:15px;
}

.finishedTask {
  text-decoration: line-through;
  color:#FFAA01;
}

.inactiveButton {
  background:#adadad;
  color:white;
}

.inactiveButton:hover {
  background:#adadad;
  color:white;
}

div.create-post {
  padding:10px;
}

div.create-post input[type=text] {
  height:25px;
  width:300px;
  margin-left:15px;
  margin-right:15px;
  border:1px solid #FFAA01;
  border-radius: 5px;
  padding: 5px 10px;
}

@media only screen and (max-width: 600px) {

  div.container {
    width:100%;
  }

  div.post {
    display:block;
    margin:10px 5px;
  }
  
  p.text {
    text-align: center;
    font-size:14px;
    width:100%;
    margin:0px;
  }

  .input-field {
    width:100%;
  }

  .delete-button, .update-button, .finish-task, .add-task-button {
  padding:10px 16px;
  margin:5px;
  font-size: 8px;
  font-weight:700;
  letter-spacing: 1px;
  text-transform:uppercase;
  color:white;
  background:#027A9F;
  border:none;
  border-radius: 5px;
  height:auto;
  width:auto;
}

div.create-post input[type=text] {
  width:80%;
  margin:0px;
}
  
}
</style>
