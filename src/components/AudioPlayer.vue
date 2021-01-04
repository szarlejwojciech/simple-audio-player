<template>
  <audio
    ref="audioplayer"
    :src="audioLink"
    @timeupdate="updateProgress"
    @loadeddata="setDuration"
  ></audio>
  <div class="player">
    <div class="controls">
      <div class="progress-btns">
        <button type="button" @click="skip($event, '-5')">⏪</button>
        <button type="button" @click="togglePlay">⏯</button>
        <button type="button" @click="skip($event, '+5')">⏩</button>
      </div>
      <div class="playback-rate-btns">
        <button
          title="wolniej"
          type="button"
          @click="changePlaybackRate($event, '-0.1')"
        >
          🔽
        </button>
        <span type="button" id="playback-rate"
          >x{{ playbackRate.toFixed(1) }}</span
        >
        <button
          title="szybciej"
          type="button"
          @click="changePlaybackRate($event, '+0.1')"
        >
          🔼
        </button>
      </div>
      <div class="time">
        <span id="current-time">{{ toMinutes(currentTime) }}</span>
        <span>/</span>
        <span id="duration">{{ toMinutes(duration) }}</span>
      </div>
    </div>
    <div
      class="progress"
      @click="changeTime"
      @mousedown="changeTime"
      @mousemove="changeTime"
      @mouseup="changeTime"
      :style="{
        backgroundImage:
          'linear-gradient(90deg, #0085ff ' +
          progress * 100 +
          '%, #ddd ' +
          progress * 100 +
          '%, #ddd )',
      }"
    ></div>
  </div>
</template>

<script>
export default {
  name: "AudioPlayer",
  props: {
    audioLink: String,
  },
  data() {
    return {
      currentTime: 0,
      duration: null,
      playbackRate: 1,
      progress: 0,
      mousedown: false,
    };
  },
  computed: {
    audio() {
      return this.$refs.audioplayer;
    },
  },
  methods: {
    setDuration({ target }) {
      this.duration = target.duration;
    },
    updateProgress({ target: { duration, currentTime } }) {
      this.currentTime = currentTime;
      this.progress = currentTime / duration;
    },
    togglePlay() {
      // console.log(audio);
      if (this.audio.paused) this.audio.play();
      else this.audio.pause();
    },
    skip(e, skipTime) {
      this.audio.currentTime += parseFloat(skipTime);
    },
    changePlaybackRate(e, rate) {
      this.playbackRate += parseFloat(rate);
      this.audio.playbackRate = this.playbackRate;
    },
    toMinutes(time) {
      const min = Math.floor(time / 60);
      const sec = Math.floor(time - 60 * min);
      return `${min}:${sec < 10 ? "0" : ""}${sec}`;
    },
    changeTime(e) {
      const scrub = (e) => {
        const time = (e.offsetX / e.target.offsetWidth) * this.audio.duration;
        this.audio.currentTime = time;
      };

      switch (e.type) {
        case "click":
          scrub(e);
          break;
        case "mousemove":
          if (this.mousedown) scrub(e);
          break;
        case "mousedown":
          this.mousedown = true;
          break;
        case "mouseup":
          this.mousedown = false;
          break;

        default:
          return null;
      }
    },
  },
};
</script>

<style scoped>
.player {
  box-sizing: border-box;
  margin: 5px auto;
  padding: 9px 13px;
  height: 50px;
  max-width: 600px;
  background-color: white;
  box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
}
.player .controls {
  display: flex;
  width: 100%;
}
.player .controls .playback-rate-btns {
  margin-left: 40px;
}
.player .controls #playback-rate {
  margin: 0 5px;
  min-width: 3em;
  display: inline-block;
}
.player .controls button {
  background-color: transparent;
  border: none;
  padding: 0;
  margin: 0;
  font-size: 1em;
  cursor: pointer;
}
.player .controls button:focus {
  outline: none;
}
.player .controls button:hover {
  opacity: 0.8;
}
.player .controls .time {
  margin: 0 0 0 auto;
  display: flex;
  align-self: flex-end;
}
.player .progress {
  height: 6px;
  background-color: #ddd;
  margin-top: 8px;
}
</style>
