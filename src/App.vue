<script setup lang="ts">
import { lrc } from './lrc';
import audio from './assets/music.mp3';

const parseTime = (time: string) => {
  const parts = time.split(':');
  return +parts[0] * 60 + +parts[1];
}

const lrcArr = lrc.split('\n').map(it => {
  const res = it.match(/(?<time>\[.*\])(?<words>.*)/);
  const { time, words } = res?.groups || {};

  return {
    time: parseTime(time?.slice(1, -1) || ''),
    words
  }
})
</script>

<template>
  <div class="container">
    <div class="audio">
      <audio :src="audio" controls></audio>
    </div>
    <div class="wrapper">
      <div class="lyric">
        <div class="lyric-li" v-for="item in lrcArr" :key="item.time">{{ item.words }}</div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
* {
  margin: 0;
  padding: 0;
}

.container {
  width: 400px;
  height: 800px;
  margin: 0 auto;
  padding: 20px 0;
}

.audio,
audio {
  width: 100%;
  height: 60px;
}

.wrapper {
  height: 600px;
  overflow: hidden;
  margin-top: 20px;
}

.lyric-li {
  text-align: center;
  list-style: none;
  font-size: 14px;
  line-height: 36px;
  transition: 0.3s ease-in;
  color: rgba(245, 245, 245, 0.4);
  cursor: pointer;
  user-select: none;
  height: 36px;
  color: rgb(18, 25, 10);
  transition: all .2s;

  &.active {
    color: rgb(255, 255, 255);
    transform: scale(1.2);
  }
}
</style>
