<template>
  <Transition>
    <div v-if="this.modalToggle" class="modal">
      <div class="modal-header">
        <div class="modal-title">Text Editor</div>
        <div>
          <button @click="this.toggleModal()">Close</button>
        </div>
      </div>
      <input v-model="editorTitle" type="text" class="editor-title" placeholder="Your title" />
      <ckeditor :editor="editor" v-model="editorData"></ckeditor>
      <div class="modal-footer">Functionality still under construction.</div>
    </div>
  </Transition>

  <header class="header">
    <img alt="Noizee" class="logo" src="./assets/logo.png" />
    <div class="btn-editor-container">
      <button @click="this.toggleModal()">Editor</button>
    </div>
  </header>

  <main class="box-container">
    <Noise
      v-for="sound in soundList"
      :key="sound.screenname"
      :sound="{
        filename: sound.filename,
        screenname: sound.screenname,
        icon: sound.icon,
      }"
    />
  </main>

  <footer class="footer">
    <br />
    <small class="text-1">
      Icons by
      <a target="_blank" href="https://www.flaticon.com/br/autores/eucalyp" title="Eucalyp">Eucalyp</a>
      <br />Developed by
      <a target="_blank" href="https://github.com/pedrocx486">pedroCX486</a>
      <br />
      <br />Issues playing? Our sounds are in MP3 format.
      <br />Linux users may
      need to install extra codecs to play them.
    </small>
  </footer>
</template>

<script>
import Noise from "./components/Noise.vue";
import ClassicEditor from "@ckeditor/ckeditor5-build-classic";

export default {
  name: "App",
  components: {
    Noise,
  },
  data() {
    return {
      editor: ClassicEditor,
      editorTitle: "",
      editorData: "<p>Content of the editor.</p>",
      modalToggle: false,
      soundList: [],
    };
  },
  methods: {
    getSoundList() {
      fetch("./assets/soundlist.json")
        .then((response) => response.json())
        .then((data) => {
          this.soundList = data;
        });
    },
    toggleModal() {
      this.modalToggle = !this.modalToggle;
    }
  },
  created() {
    this.getSoundList();
  },
};
</script>

<style scoped>
@import "./assets/base.css";

.logo {
  max-width: 60%;
  height: auto;
}

.box-container {
  text-align: center;
  max-width: 40rem;
  margin-left: auto;
  margin-right: auto;
}

.header {
  text-align: center;
  position: relative;
  animation: fadeInOpacity 600ms;
}

.btn-editor-container {
  position: absolute;
  left: 0.5rem;
  top: 0.5rem;
}

.footer {
  text-align: center;
  padding: 5px;
  animation: fadeInOpacity 600ms;
}

.editor-title {
  border: none;
  width: 97.9%;
  padding: 0.5rem;
  font-size: x-large;
}

.modal {
  position: absolute;
  left: 0;
  right: 0;
  margin: 0 auto;
  height: 60%;
  width: 80%;
  z-index: 2;
  background-color: white;
  border-radius: 5px;
  margin-top: 2rem;
}

.modal-header {
  height: 3rem;
  display: flex;
  flex-direction: row;
  align-content: center;
  justify-content: space-between;
  align-items: center;
  padding: 0.8rem;
  color: black;
  border-bottom: solid 1px lightgray;
}

.modal-title {
  font-size: x-large;
  font-weight: 600;
}

.modal-footer {
  background-color: white;
  border-bottom-left-radius: 5px;
  border-bottom-right-radius: 5px;
  padding: 0.5rem;
  color: black;
}
</style>
