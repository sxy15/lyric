<script setup lang="ts">
import { lrc } from './lrc';
import audio from './assets/music.mp3';
import { onMounted, reactive, ref } from 'vue';

let activeIndex = ref(0);

let doms = reactive({
  audio: null as HTMLAudioElement | null,
  wrapper: null as HTMLElement | null,
  lyric: null as HTMLElement | null
})

let heights = reactive({
  wrapper: 0,
  lyric: 0,
  item: 0
})

const parseTime = (time: string) => {
  const parts = time.split(':');
  return +parts[0] * 60 + +parts[1];
}

const findActiveIndex = () => {
  const currentTime = doms.audio?.currentTime || 0;
  const index = lrcArr.findIndex(it => it.time > currentTime);
  return index === -1 ? lrcArr.length - 1 : index - 1;
}

const lrcArr = lrc.split('\n').map(it => {
  const res = it.match(/(?<time>\[.*\])(?<words>.*)/);
  const { time, words } = res?.groups || {};

  return {
    time: parseTime(time?.slice(1, -1) || ''),
    words
  }
})

const setOffset = () => {
  const offset = heights.item * Math.max(activeIndex.value - 5, 0);
  const maxOffset = heights.lyric - heights.wrapper;
  const computedOffset = offset > maxOffset ? maxOffset : offset;
  (doms.lyric as HTMLElement).style.transform = `translateY(-${computedOffset}px)`
}

onMounted(() => {
  const audioDom = document.querySelector('audio') as HTMLAudioElement;
  const wrapperDom = document.querySelector('.wrapper') as HTMLElement;
  const lyricDom = document.querySelector('.lyric') as HTMLElement;
  doms = {
    audio: audioDom,
    wrapper: wrapperDom,
    lyric: lyricDom
  }
  const wrapperHeight = doms.wrapper?.clientHeight || 0;
  const lyricHeight = doms.lyric?.clientHeight || 0;
  const itemHeight = lyricHeight / lrcArr.length;
  heights = {
    wrapper: wrapperHeight,
    lyric: lyricHeight,
    item: itemHeight,
  }
  doms.audio?.addEventListener('timeupdate', () => {
    const idx = findActiveIndex()
    activeIndex.value = idx;
    setOffset()
  })
})
</script>

<template>
  <div class="container">
    <div class="audio">
      <audio :src="audio" controls></audio>
    </div>
    <div class="wrapper">
      <div class="lyric">
        <div :class="['lyric-li', activeIndex === i && 'active']" v-for="(item, i) in lrcArr" :key="item.time">
          {{ item.words }}</div>
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

.lyric {
  transition: all 0.2s;
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
    color: rgb(174 81 0);
    transform: scale(1.2);
  }
}
</style>
