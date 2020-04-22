<template>
  <div>
   <section class="section header">
  
    <div class="container">
      <h1 class="title is-size-3 has-text-white">
        Q-Flix
      </h1>
      <h2 class="subtitle has-text-white">
        Endless Entertainment
      </h2>
    </div>
  
</section>

<div class="container">
  <div class="banner">
    <h1><strong>Upload Movie</strong></h1>
     <v-alert v-if="uploading" type="info">
      Movie Uploading. This Message Will Dissapear Once Upload Is Complete. Please Wait.
    </v-alert>
  </div>
</div>

<section class="section">
    <div class="container">

      <div class="container">
        <div class="columns is-multiline">

          <div class="column is-6">
            <div class='is-flex is-horizontal-center'>
            <figure class="image size">
        <img  src="../assets/db.png">
      </figure>
            </div>
            <h1 class="subtitle"><strong>Upload any movie from your PC, that is configured to connect to this local network, straight to the server's database for viewing later!</strong></h1>
          </div>

          <div class="column is-6">
            <div class="field">
              <label class="label has-text-black">Movie Title</label>
              <div class="control">
                <input v-model="movie_upload.title" class="input" type="text" placeholder="Enter Movie Title *">
              </div>
            </div>
            <div class="field">
              <label class="label">Movie Description</label>
              <div class="control">
                <textarea v-model="movie_upload.description" class="textarea" placeholder="Enter Movie Description"></textarea>
              </div>
            </div>
            

            <div class="columns">
              <div class="column is-6">
                <b-field class="file" accept="image/*">      
              <b-upload v-model="movie_upload.img_file">
                  <a class="button buttons">
                   
                    <span>Click To Upload Thumbnail</span>
                </a>
              </b-upload>
          </b-field>
              </div>
              <div class="column is-6">
                <b-field class="file" accept="video/*">      
              <b-upload v-model="movie_upload.movie_file">
                  <a class="button buttons">
                   
                    <span>Click To Upload Movie</span>
                </a>
              </b-upload>
          </b-field>
              </div>
            </div>
            
          <div class="buttons">
            <b-button @click="upload" class="button" expanded>Upload Movie</b-button>
        </div>
        
          </div>


        </div>
      </div>
      
    </div>
  </section>


  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Home',
  
  data(){
    return{
      movie_upload:{
        title:'',
        description:'',
        img_file:null,
        movie_file:null
      },
      uploading:false
    }
  },
  methods:{
    upload(){

      let reader = new FileReader()

      reader.onload = function(event) {
        
        this.movie_upload.img_file = event.target.result

        console.log(this.movie_upload.img_file)

        const file = new FormData();

        console.log(this.movie_upload.movie_file.name + this.movie_upload.title)

        file.set('title',this.movie_upload.title)
        file.set('description',this.movie_upload.description)
        file.set('thumb', this.movie_upload.img_file);
        file.append('movie', this.movie_upload.movie_file);

        this.uploading = true
        axios.post('http://192.168.1.112:9000/api/q-flix/upload_movie', file).then( response => {

          console.log(response)
          this.uploading = false

        })


      }.bind(this);

      reader.readAsDataURL(this.movie_upload.img_file)


    }
  }
}
</script>

<style scoped>
.header{
  text-align: center;
  vertical-align: middle;
  background-color: #000000;
background-image: linear-gradient(315deg, #000000 0%, #b82e1f 74%);
  color:white;
}

.size{
  width:200px;
  height: 200px;
}

.button{
  background-color: #000000;
background-image: linear-gradient(315deg, #000000 0%, #b82e1f 74%);
  color: white;
  font-weight: bold;

}

.button{
  background-color: #000000;
background-image: linear-gradient(315deg, #000000 0%, #b82e1f 74%);
  color: white;
  font-weight: bold;
  float:right;
}
.banner{
  width:100%;
  padding:10px;
  font-size: 25px;
  text-align: center;
}

.is-horizontal-center {
  justify-content: center;
  padding-bottom:15px;
}

</style>
