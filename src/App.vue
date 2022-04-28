<script setup lang="ts">
import { ref, onMounted, inject } from 'vue'
import { QuillEditor } from "@vueup/vue-quill";
import "@vueup/vue-quill/dist/vue-quill.snow.css";
import { PencilOutline, CloseThick, VolumeOff } from "mdue";
import Noise from './components/Noise.vue';

const emitter: any = inject('emitter');

let modalToggle = ref(false);
let editorTitle = ref('');
let editorContent = ref('Your ideas.');
let soundList = ref([]);
let showSaved = ref(false);

const getSoundList = () => {
  fetch("./assets/soundlist.json")
    .then((response) => response.json())
    .then((data) => {
      soundList.value = data;
    });
}

const toggleModal = () => {
  modalToggle.value = !modalToggle.value;
}

const muteAll = () => {
  emitter.emit("mute");
}

const storageSave = () => {
  showSaved.value = true;
  localStorage.setItem(
    "noizeeTextEditor",
    JSON.stringify({ title: editorTitle.value, content: editorContent.value })
  );
  setTimeout(() => {
    showSaved.value = false;
  }, 1000);
}

const storageLoad = () => {
  if (!!localStorage.getItem("noizeeTextEditor")) {
    let storageData = JSON.parse(localStorage.getItem("noizeeTextEditor"));
    editorTitle.value = storageData.title;
    editorContent.value = storageData.content;
  }
}

const printFromEditor = () => {
  const printWindow = window.open("", "Noizee", "height=720,width=1280");
  printWindow.document.write(
    "<html><head><title>" + editorTitle.value + "</title>"
  );
  printWindow.document.write("</head><body >");
  printWindow.document.write("<h1>" + editorTitle.value + "</h1>");
  printWindow.document.write(editorContent.value);
  printWindow.document.write("</body></html>");
  printWindow.document.close();
  printWindow.focus();
  printWindow.print();
  printWindow.close();
}

const resetEditor = () => {
  editorTitle.value = "";
  editorContent.value = "Your new ideas.";
  storageSave();
}

const createDebounce = () => {
  let timeout = null;
  return function (fnc: Function, delayMs: number) {
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      fnc();
    }, delayMs || 400);
  };
}

onMounted(() => {
  getSoundList();
  storageLoad();
});

let debounce = createDebounce();
</script>

<template>
  <div>
    <Transition>
      <div v-if="modalToggle" class="modal">
        <div class="modal-header">
          <div class="modal-title">Text Editor</div>
          <div>
            <close-thick class="md-normal-icon-size btn-animated btn-close" @click="toggleModal()"></close-thick>
          </div>
        </div>
        <div class="modal-editor-title">
          <input v-model="editorTitle" type="text" class="editor-title" placeholder="Your title"
            @keydown="debounce(() => { storageSave(); })" />
        </div>
        <div class="modal-editor">
          <QuillEditor v-model:content="editorContent" contentType="html" @keydown="debounce(() => { storageSave(); })"
            theme="snow" />
        </div>
        <div class="modal-footer">
          <small>Content autosaves to the browser's local storage.</small>
          <br />
          <button class="custom-btn btn-info" @click="printFromEditor()">Print to File/Printer</button>
          &nbsp;
          <button class="custom-btn btn-danger" @click="resetEditor()">Reset Editor</button>
          &nbsp;
          <div class="powered-by">
            <small>
              <Transition>
                <span class="saved-hint" v-if="showSaved">Contents saved. &nbsp;</span>
              </Transition>Powered by
              <a href="https://vueup.github.io/vue-quill/">QuillEditor</a>.
            </small>
          </div>
        </div>
      </div>
    </Transition>

    <header class="header">
      <img alt="Noizee" class="logo" src="./assets/logo.png" />
      <div class="btn-editor-container">
        <pencil-outline class="md-normal-icon-size btn-animated btn-editor" @click="toggleModal()">
        </pencil-outline>
      </div>
      <div class="btn-mute-container">
        <volume-off class="md-normal-icon-size btn-animated btn-mute" @click="muteAll()"></volume-off>
      </div>
    </header>

    <main class="box-container">
      <Noise v-for="sound in soundList" :key="sound.screenname" :sound="{
        filename: sound.filename,
        screenname: sound.screenname,
        icon: sound.icon,
      }" />
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
        <br />Linux users may need to install extra codecs to play them.
      </small>
    </footer>
  </div>
</template>

<style>
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

.modal-editor-title {
  width: 100%;
}

.editor-title {
  border: none;
  padding: 0.5rem;
  font-size: x-large;
  width: -webkit-fill-available;
}

.modal-editor {
  height: 24rem;
  background: white;
  overflow: hidden;
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
  position: relative;
  bottom: 1rem;
  right: 1rem;
}

.saved-hint {
  color: rgb(202, 202, 202);
}
</style>
