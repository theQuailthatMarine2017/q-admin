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

  <div class="container">
  <div class="banner">
    <h1><strong>Manage Movie Collection</strong></h1>
  </div>
</div>

<div class="container" v-if="movies === null">
  <div class="banner">
    <h1><strong>No Movies Found in DB</strong></h1>
  </div>
  </div>

<section class="section">
    <div class="container">

      <div class="container">
        <div class="columns is-multiline">

             <vue-good-table
                  @on-selected-rows-change="selectionChanged"
                  :select-options="{ enabled: true }"
                  :search-options="{ enabled: true,placeholder:'Find Movie' }"
                  theme="black-rhino"
                  :columns="columns"
                  :rows="movies"
                  v-if="movies != null"
                  ref="my-table"
                  :pagination-options="{
                      enabled: true,
                      mode: 'records',
                      perPage: 5,
                      position: 'bottom',
                      perPageDropdown: [3, 7, 9],
                      dropdownAllowAll: false,
                      setCurrentPage: 2,
                      nextLabel: 'next',
                      prevLabel: 'prev',
                      rowsPerPageLabel: 'Rows per page',
                      ofLabel: 'of',
                      pageLabel: 'page', // for 'pages' mode
                      allLabel: 'All',
                    }"
                  ><div slot="selected-row-actions">
                    <div class="my-2">
                      <b-button @click="del">Delete Movie</b-button>
                      </div>
                    </div></vue-good-table>
        
          </div>
        </div>
      </div>
    </section>

  <modal name="uploading-movie" :clickToClose="false">
    <div class='is-flex is-horizontal-center'>
   <figure class="image uploading-img">
        <img  src="../assets/uploading-icon.png">
      </figure>
    </div>
  </modal>

   <b-modal :active.sync="isCardModalActive">
    <section>
            <div class="card" style="padding:50px;">
                <b-field label="Movie Title">
            <b-input :value="update_movie.title"></b-input>
        </b-field>
        <b-field label="Description">
            <b-input type="textarea" :value="update_movie.description"></b-input>
        </b-field>
        
            
                <b-button @click="upd">Update Movie</b-button>
            
        
            </div>
          </section>
        </b-modal>

  </div>
</template>

<script>
import axios from 'axios';
import 'vue-good-table/dist/vue-good-table.css'
import { VueGoodTable } from 'vue-good-table';


export default {
  name: 'Home',
  components: { 

    VueGoodTable 
  },
  mounted(){

    this.getmovies()
  },
  data(){
    return{
      movie_upload:{
        title:'',
        description:'',
        img_file:null,
        movie_file:null
      },
      thumb_file:null,
      movie_file:null,
      isCardModalActive:false,
      update_movie:{
        title:'',
        description:''
      },
      movie_to_update_title:'',
      movie_to_update_description:'',
      uploading:false,
      prog:0,
      movies:null,
      columns:[{
          label: 'Title',
          field: 'title',
        },
        {
          label: 'Description',
          field: 'description'
        },
        {
          label: 'Created On',
          field: 'createdAt'
        }]
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


        this.$modal.show('uploading-movie');

        //CONFIG TO SHOW UPLOAD PROGRESS
        //const config = {
            //onUploadProgress: function(progressEvent) {


              //var percentCompleted = Math.round((progressEvent.loaded * 100) / progressEvent.total)
              //console.log(percentCompleted)

            //}
          //}

        axios.post('http://192.168.1.112:9000/api/q-flix/upload_movie', file).then( response => {

          this.$modal.hide('uploading-movie');

          console.log(response)

          this.movie_upload = null

        })


      }.bind(this);

      reader.readAsDataURL(this.movie_upload.img_file)


    },
      onProgress(percent){
      //update vue
      this.prog += percent

    },
    selectionChanged(){

      if(this.$refs['my-table'].selectedRows[0].title === 'undefined'){

        console.log('no movie selected')

      }else{

        if(this.movie_to_update_title === '' && this.movie_to_update_description === ''){

         this.movie_to_update_title = this.$refs['my-table'].selectedRows[0].title
         this.movie_to_update_description = this.$refs['my-table'].selectedRows[0].description

        } else {

           this.movie_to_update_title = ''
           this.movie_to_update_description = ''
           this.movie_to_update_title = this.$refs['my-table'].selectedRows[0].title
           this.movie_to_update_description = this.$refs['my-table'].selectedRows[0].description
        }

      }

    },
    getmovies(){

            //this.isLoading = true;

            axios.get('http://192.168.1.112:9000/api/q-flix/movies').then( response => {

              this.movies =  response.data
              //this.isLoading = false
              console.log("Movies" + this.movies[0])

            })
            
        },
    //upd(){

            //console.log(this.movie_to_update_description)

            //this.update_movie.title = this.movie_to_update_title
            //this.update_movie.description = this.movie_to_update_description

            //console.log(this.update_movie)
//
            //axios.put('http://192.168.1.112:9000/api/q-flix/update/', this.update_movie).then( response => {


                //this.$modal.hide('updating-movie');

                //console.log(response)

                //this.update_movie = null
                //this.movie_to_update_title = ''
                //this.movie_to_update_description = ''

                //this.getmovies()

            //})


        //},
    del(){

        console.log(this.movie_to_update_title);

        this.$swal.fire({

              title: 'Are you sure you want to delete ' + this.movie_to_update_title + '?',
              text: "You won't be able to revert this!",
              icon: 'warning',
              showCancelButton: true,
              confirmButtonColor: '#3085d6',
              cancelButtonColor: '#d33',
              confirmButtonText: 'Yes, delete it!'

            }).then((result) => {

              if (result.value) {

                axios.delete('http://192.168.1.112:9000/api/q-flix/delete/' + this.movie_to_update_title).then( response => {

                  console.log(response)
                  if (response != null) {
                    this.$swal.fire(
                      'Deleted!',
                      'Your file has been deleted.',
                      'success'
                    )

                    this.getmovies()

                  }

                })

                this.$swal.fire(
                  'Deleted!',
                  'Your file has been deleted.',
                  'success'
                )
              }
            })

      }
  },
  
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

.point:hover{
  cursor:pointer;
}
.uploading-img{
  width:300px;
  height:300px;
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
