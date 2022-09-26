<script setup lang="ts">
import { ref, onMounted, inject } from "vue";
import { PencilOutline, VolumeOff } from "mdue";
import Editor from "./components/Editor.vue";
import "@vueup/vue-quill/dist/vue-quill.snow.css";
import Sound from "./components/Sound.vue";

const emitter: any = inject("emitter");

let modalToggle = ref(false);
let soundList = ref([]);

const getSoundList = () => {
  fetch("./assets/soundlist.json")
    .then((response) => response.json())
    .then((data) => {
      soundList.value = data;
    });
};

const toggleModal = () => {
  modalToggle.value = !modalToggle.value;
};

const muteAll = () => {
  emitter.emit("mute");
};

onMounted(() => {
  getSoundList();

  emitter.on("closeModal", () => {
    toggleModal();
  });
});
</script>

<template>
  <div>
    <Transition>
      <Editor v-if="modalToggle" />
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
      <Sound v-for="sound in soundList" :key="sound.screenname" :sound="{
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
        <br />
        Sounds by unknown
        <br />
        Developed by
        <a target="_blank" href="https://github.com/pedrocx486">pedroCX486</a>
        <br />
        <br />Issues playing? Our sounds are in MP3 format. <br />Linux users
        may need to install extra codecs to play them.
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
</style>
