<template>
  <div class="sound-box">
    <audio loop preload="none" :id="this.sound.filename">
      <source v-bind:src="'./assets/sounds/' + this.sound.filename + '.mp3'" type="audio/mpeg" />
    </audio>

    <div @click="this.audioControls()">
      <img v-bind:src="'./assets/icons/' + this.sound.icon" class="sound-icon" />
      <br />
      <div class="audio-info">
        <small class="text-1">{{ this.sound.screenname }}</small>&nbsp;
        <div class="playing-icon-box">
          <Transition>
            <play v-if="this.isPlaying" class="playing-icon md-normal-icon-size"></play>
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
import { Play } from "mdue";

export default {
  name: "Noise",
  components: { Play },
  props: {
    sound: { filename: "", screennanme: "", icon: "" },
  },
  data() {
    return {
      isPlaying: false,
    };
  },
  methods: {
    volumeControls() {
      document.getElementById(this.sound.filename).volume =
        document.getElementById(this.sound.filename + "-volume").value;
    },
    audioControls() {
      const soundElement = document.getElementById(this.sound.filename);

      if (soundElement.paused) {
        this.volumeControls();
        this.isPlaying = true;
        soundElement.play();
      } else {
        this.isPlaying = false;
        soundElement.pause();
      }
    },
    mute() {
      if (this.isPlaying) {
        this.audioControls();
      }
    },
  },
  mounted() {
    this.emitter.on("mute", () => {
      this.mute();
    });
  },
};
</script>

<style>
.audio-info {
  position: relative;
  display: inline-flex;
}

.playing-icon-box {
  position: absolute;
  right: -15px;
  margin-left: 10px;
  top: 2px;
}

.sound-icon {
  width: 64px;
  height: 64px;
  cursor: pointer;
}

.playing-icon {
  width: 15px;
  height: 15px;
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
