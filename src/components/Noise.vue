<script setup lang="ts">
import { ref, onMounted, inject } from 'vue'
import { Play } from "mdue";

const emitter: any = inject('emitter');

const props = defineProps<{ sound: { filename: string, screenname: string, icon: string } }>()

let isPlaying = ref(false);

const volumeControls = () => {
  (document.getElementById(props.sound.filename) as HTMLAudioElement).volume =
    Number((document.getElementById(props.sound.filename + "-volume") as HTMLInputElement)!.value);
}

const audioControls = () => {
  const soundElement = <HTMLAudioElement>document.getElementById(props.sound.filename)!;

  if (soundElement.paused) {
    volumeControls();
    isPlaying.value = true;
    soundElement.play();
  } else {
    isPlaying.value = false;
    soundElement.pause();
  }
}

const mute = () => {
  if (isPlaying.value) {
    audioControls();
  }
}

onMounted(() => {
  emitter.on("mute", () => {
    mute();
  });
});
</script>

<template>
  <div class="sound-box">
    <audio loop preload="none" :id="sound.filename">
      <source v-bind:src="'./assets/sounds/' + sound.filename + '.mp3'" type="audio/mpeg" />
    </audio>

    <div @click="audioControls()">
      <img v-bind:src="'./assets/icons/' + sound.icon" class="sound-icon" />
      <br />
      <div class="audio-info">
        <small class="text-1">{{ sound.screenname }}</small>&nbsp;
        <div class="playing-icon-box">
          <Transition>
            <play v-if="isPlaying" class="playing-icon md-normal-icon-size"></play>
          </Transition>
        </div>
      </div>
      <br />
    </div>
    <input @input="volumeControls(sound.filename)" type="range" min="0" max="1" step="0.01" value="0.30"
      :id="sound.filename + '-volume'" v-bind:title="'Volume Control for ' + sound.screenname" />
  </div>
</template>

<style scoped>
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
