<template >
  <div id="app">
    <NavBar />
    <div class="contenedor">
      <div class="nota">
        <b-card title="Nota destacada" id="card" class="mb-2">
          <b-form @submit.prevent="agregarNota" class="d-flex flex-column">
            <b-form-group
              id="input-group-1"
              label="Título"
              label-for="title"
              description="Título de la noticia destacada"
            >
              <b-form-input
                id="title"
                v-model="newWebsite.titulo"
              ></b-form-input>
            </b-form-group>

            <b-form-group
              id="input-group-2"
              label="Cuerpo"
              label-for="description"
              description="Cuerpo de la noticia destacada"
            >
              <b-form-textarea
                id="description"
                v-model="newWebsite.cuerpo"
                rows="3"
              ></b-form-textarea>
            </b-form-group>
            <b-form-group
              id="input-group-3"
              label="Link"
              label-for="url"
              description="Link de la noticia destacada"
            >
              <b-form-input id="url" v-model="newWebsite.link"></b-form-input>
            </b-form-group>
            <input
              @change="imagen($event)"
              class="form-control mt-3"
              type="file"
              ref="myFile"
            />
            <b-button class="mt-3" variant="primary" type="submit"
              >Subir Nota</b-button
            >
          </b-form>
        </b-card>
      </div>
      <div class="spotify">
        <b-card title="Playlist Spotify" id="card2" class="mb-2">
          <b-form class="d-flex flex-column" @submit.prevent="agregarPlaylist">
            <b-form-group
              id="input-group-1"
              label="Playlist link"
              label-for="spotify-link"
              description="Link de la playlist de Spotify"
            >
              <b-form-input id="spotify-link" v-model="spotify"></b-form-input>
            </b-form-group>

            <b-button class="mt-3" type="submit" variant="primary"
              >Subir Playlist</b-button
            >
          </b-form>
        </b-card>
      </div>
    </div>
  </div>
</template>

<script>
import Firebase from "firebase";
import "firebase/storage";
import config from "./config";
import NavBar from "./components/NavBar.vue";
let app = Firebase.initializeApp(config);
let db = app.database();
let websitesRef = db.ref("nota");
let playlist = db.ref("musica");
let storage = app.storage();
let storageRef = storage.ref();

export default {
  name: "App",
  firebase: {
    nota: websitesRef,
    musica: playlist,
  },
  data() {
    return {
      newWebsite: {
        titulo: "",
        cuerpo: "",
        foto: "",
        link: "",
      },
      spotify: "",
    };
  },
  methods: {
    agregarNota() {
      db.ref("nota").remove();
      websitesRef.push(this.newWebsite);
      this.newWebsite.titulo = "";
      this.newWebsite.cuerpo = "";
      this.newWebsite.link = "";
      this.eliminarImagen();
      this.subirImagen();
    },

    agregarPlaylist() {
      db.ref("musica").remove();
      playlist.push(this.spotify);
      this.spotify = "";
    },

    imagen(e) {
      this.newWebsite.foto = e.target.files[0];
      console.log(this.newWebsite.foto);
    },

    subirImagen() {
      let imagesRef = storageRef.child("imagenes/" + this.newWebsite.foto.name);
      imagesRef.put(this.newWebsite.foto).then((e) => {
        console.log(e);
      });
    },

    eliminarImagen() {
      // Obtener una referencia al directorio que contiene las imágenes
      let imagesRef = storageRef.child("imagenes/");

      // Obtener una lista de todos los archivos en el directorio
      imagesRef
        .listAll()
        .then((result) => {
          // Recorrer la lista y eliminar cada archivo
          result.items.forEach((imageRef) => {
            imageRef
              .delete()
              .then(() => {
                // Imprimir un mensaje en la consola cuando se elimina cada imagen
                console.log(`Imagen eliminada: ${imageRef.name}`);
              })
              .catch((error) => {
                // Imprimir un mensaje de error en la consola si ocurre un problema al eliminar una imagen
                console.error(
                  `Error al eliminar la imagen ${imageRef.name}: ${error}`
                );
              });
          });
        })
        .catch((error) => {
          // Imprimir un mensaje de error en la consola si ocurre un problema al obtener la lista de imágenes
          console.error(`Error al obtener la lista de imágenes: ${error}`);
        });
    },
  },
  components: {
    NavBar,
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500&display=swap");

#app {
  font-family: "Poppins", sans-serif;
  font-weight: 400;
  background-size: cover;
  background-image: linear-gradient(
    to right top,
    #141414,
    #141415,
    #151416,
    #151516,
    #151517,
    #161618,
    #161719,
    #17181a,
    #181a1b,
    #191c1d,
    #1b1d1e,
    #1d1f1f
  );
  height: 100vh;
  width: 100vw;
  margin: 0;
  padding: 0;
}

.contenedor {
  display: flex;
  flex-direction: row;
  justify-content: center;
  justify-content: space-evenly;
}

@media screen and (max-width: 768px) {
  #app {
    height: 100%;
    width: 100%;
  }

  .contenedor {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .nota {
    width: 95%;
  }

  .spotify {
    width: 95%;
    margin-bottom: 3%;
  }
}

#card {
  background: #ecebee; /* fallback for old browsers */
}

#card2 {
  background: #ecebee; /* fallback for old browsers */
  height: fit-content;
}
</style>
