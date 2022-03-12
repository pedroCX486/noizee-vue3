<template>
  <div id="app">
    <Transition>
      <div v-if="this.modalToggle" class="modal">
        <div class="modal-header">
          <div class="modal-title">Text Editor</div>
          <div>
            <close-thick class="md-normal-icon-size btn-animated btn-close" @click="this.toggleModal()"></close-thick>
          </div>
        </div>
        <input
          v-model="editorTitle"
          type="text"
          class="editor-title"
          placeholder="Your title"
          @keydown="debounce(() => { storageSave(); })"
        />
        <div class="modal-editor">
          <QuillEditor
            v-model:content="editorContent"
            contentType="html"
            @keydown="debounce(() => { storageSave(); })"
            theme="snow"
          />
        </div>
        <div class="modal-footer">
          <small>
            Content autosaves to the browser's local storage.
          <Transition>
            <span class="saved-hint" v-if="showSaved">Contents saved.</span>
          </Transition>
          </small>
          <br/>
          <button class="custom-btn btn-info" @click="printFromEditor()">Print to File/Printer</button>
          &nbsp;
          <button class="custom-btn btn-danger" @click="resetEditor()">Reset Editor</button>
          &nbsp;
          <div class="powered-by">Powered by <a href="https://vueup.github.io/vue-quill/">QuillEditor</a>.</div>
        </div>
      </div>
    </Transition>

    <header class="header">
      <img
        alt="Noizee"
        class="logo"
        src="./assets/logo.png"
      />
      <div class="btn-editor-container">
        <pencil-outline class="md-normal-icon-size btn-animated btn-editor" @click="this.toggleModal()"></pencil-outline>
      </div>
      <div class="btn-mute-container">
        <volume-off class="md-normal-icon-size btn-animated btn-mute" @click="this.muteAll()"></volume-off>
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
        <a target="_blank" href="https://www.flaticon.com/br/autores/eucalyp" title="Eucalyp">
          Eucalyp
        </a>
        <br />Developed by
        <a target="_blank" href="https://github.com/pedrocx486">
          pedroCX486
        </a>
        <br />
        <br />Issues playing? Our sounds are in MP3 format.
        <br />Linux users may need to install extra codecs to play them.
      </small>
    </footer>
  </div>
</template>

<script>
import Noise from "./components/Noise.vue";

import { QuillEditor } from "@vueup/vue-quill";
import "@vueup/vue-quill/dist/vue-quill.snow.css";

import { PencilOutline, CloseThick, VolumeOff } from "mdue";

export default {
  name: "App",
  components: {
    Noise,
    QuillEditor,
    PencilOutline,
    CloseThick,
    VolumeOff,
  },
  data() {
    return {
      modalToggle: false,
      editorTitle: "",
      editorContent: "Your ideas.",
      soundList: [],
      debounce: this.createDebounce(),
      showSaved: false,
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
    },
    muteAll() {
      this.emitter.emit("mute");
    },
    storageSave() {
      this.showSaved = true;
      localStorage.setItem(
        "noizeeTextEditor",
        JSON.stringify({ title: this.editorTitle, content: this.editorContent })
      );

      setTimeout(() => {
        this.showSaved = false;
      }, 1000);
    },
    storageLoad() {
      if (!!localStorage.getItem("noizeeTextEditor")) {
        let storageData = JSON.parse(localStorage.getItem("noizeeTextEditor"));
        this.editorTitle = storageData.title;
        this.editorContent = storageData.content;
      }
    },
    printFromEditor() {
      const printWindow = window.open("", "Noizee", "height=720,width=1280");

      printWindow.document.write(
        "<html><head><title>" + this.editorTitle + "</title>"
      );
      printWindow.document.write("</head><body >");
      printWindow.document.write("<h1>" + this.editorTitle + "</h1>");
      printWindow.document.write(this.editorContent);
      printWindow.document.write("</body></html>");

      printWindow.document.close();
      printWindow.focus();

      printWindow.print();
      printWindow.close();
    },
    resetEditor() {
      this.editorTitle = "";
      this.editorContent = "Your new ideas.";
      this.storageSave();
    },
    createDebounce() {
      let timeout = null;
      return function (fnc, delayMs) {
        clearTimeout(timeout);
        timeout = setTimeout(() => {
          fnc();
        }, delayMs || 400);
      };
    },
  },
  created() {
    this.getSoundList();
    this.storageLoad();
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

.btn-animated {
  transition: ease-in 300ms;
  animation: fadein 600ms;
}

.btn-editor-container {
  position: absolute;
  left: 0.5rem;
  top: 0.5rem;
}

.btn-editor:hover {
  color: slateblue;
  transition: ease-in 300ms;
  cursor: pointer;
}

.btn-mute-container {
  position: absolute;
  right: 0.5rem;
  top: 0.5rem;
}

.btn-mute:hover {
  color: crimson;
  transition: ease-in 300ms;
  cursor: pointer;
}

.footer {
  text-align: center;
  padding: 5px;
  animation: fadeInOpacity 600ms;
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

.btn-close:hover {
  color: crimson;
  transition: ease-in 300ms;
  cursor: pointer;
}

.modal-title {
  font-size: x-large;
  font-weight: 600;
}

.editor-title {
  border: none;
  width: 97.9%;
  padding: 0.5rem;
  font-size: x-large;
}

.modal-editor {
  height: 24rem;
}

.modal-footer {
  background-color: white;
  border-bottom-left-radius: 5px;
  border-bottom-right-radius: 5px;
  padding: 0.5rem;
  padding-top: 3rem;
  color: black;
}

.powered-by {
  text-align: right;
  position: absolute;
  bottom: -6rem;
  right: 1rem;
}

.saved-hint {
  color: gray;
}
</style>
