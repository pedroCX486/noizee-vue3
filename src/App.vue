<template>
  <header class="header">
    <img alt="Noizee" class="logo" src="./assets/logo.png" />
  </header>

  <main class="box-container">
    <Noise
      v-for="sound in soundList"
      :key="sound.screenname"
      :sound="{ filename: sound.filename, screenname: sound.screenname, icon: sound.icon }"
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
      <br />Linux users may need to install extra codecs to play them.
    </small>
  </footer>
</template>

<script>
import Noise from "./components/Noise.vue";

export default {
  name: 'App',
  components: {
    Noise
  },
  data() {
    return {
      soundList: []
    }
  },
  methods: {
    getSoundList() {
      fetch("./assets/soundlist.json").then(response => response.json()).then(data => {
        this.soundList = data;
      });
    }
  },
  created() {
    this.getSoundList();
  }
}
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

.footer {
  text-align: center;
  padding: 5px;
  animation: fadeInOpacity 600ms;
}
</style>
