<template>
  <div class="sound-box">
    <audio loop preload="none" :id="this.sound.filename">
      <source v-bind:src="'./assets/sounds/' + this.sound.filename + '.mp3'" type="audio/mpeg" />
    </audio>

    <div @click="this.audioControls(this.sound.filename)">
      <img v-bind:src="'./assets/icons/' + this.sound.icon" class="sound-icon" />
      <br />
      <div class="audio-info">
        <small class="text-1">{{ this.sound.screenname }}</small>&nbsp;
        <div class="playing-icon-box">
          <Transition>
            <img v-if="this.isPlaying" v-bind:src="'./assets/icons/playing.svg'" class="playing-icon" />
          </Transition>
        </div>
      </div>
      <br />
    </div>
    <input
      @input="this.volumeControls(this.sound.filename)"
      type="range"
      min="0"
      max="1"
      step="0.01"
      value="0.30"
      :id="this.sound.filename + '-volume'"
      v-bind:title="'Volume Control for ' + this.sound.screenname"
    />
  </div>
</template>

<script>
export default {
  name: 'Noise',
  props: {
    sound: { filename: '', screennanme: '', icon: '' }
  },
  data() {
    return {
      isPlaying: false
    }
  },
  methods: {
    volumeControls(sound) {
      document.getElementById(sound).volume = document.getElementById(sound + '-volume').value;
    },
    audioControls(sound) {
      const soundElement = document.getElementById(sound);

      if (soundElement.paused) {
        this.volumeControls(sound);
        this.isPlaying = true;
        soundElement.play();
      } else {
        this.isPlaying = false;
        soundElement.pause();
      }
    }
  }
}
</script>

<style scoped>
.audio-info {
  position: relative;
  display: inline-flex;
}

.playing-icon-box {
  position: absolute;
  right: -10px;
  margin-left: 10px;
}

.sound-icon {
  width: 64px;
  height: 64px;
  cursor: pointer;
}

.playing-icon {
  width: 8px;
  height: 8px;
  margin-bottom: 2px !important;
  opacity: 1;
}

.sound-box {
  text-align: center;
  width: 190px;
  display: inline-block;
  padding: 5px;
  margin-top: 2rem;
  animation: fadeInOpacity 600ms;
}
</style>
