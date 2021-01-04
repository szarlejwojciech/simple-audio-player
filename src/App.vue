<template>
  <AudioPlayer
    audioLink="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3"
  />
</template>

<script>
import AudioPlayer from "./components/AudioPlayer.vue";
export default {
  name: "App",
  components: {
    AudioPlayer,
  },
};
</script>

<style>
</style>
