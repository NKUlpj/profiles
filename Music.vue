<script setup>
import { ref, reactive, onMounted, watch } from 'vue';
const music_state = reactive({
    list: [],
    is_play: 0,
    is_paused: 0,
    cur_id: 0,
    cur_time: 0.565508,
    audio: undefined
});

const music_wrapper_show = ref(0);



onMounted(() => {

    music_state.list = [
        {
            "id": 0,
            "title": "这世界有那么多人",
            "author": "莫文蔚",
            "url": "https://qiniu.hwater.site/setting/zheshijienameduoren.mp3",
            "cover": "https://qiniu.hwater.site/setting/zheshijieyounameduoren.webp"
        },
        {
            "id": 1,
            "title": "断了的弦",
            "author": "周杰伦",
            "url": "https://qiniu.hwater.site/setting/duanledexian.mp3",
            "cover": "https://qiniu.hwater.site/custom/1678515328712.png"
        },
        {
            "id": 2,
            "title": "半岛铁盒",
            "author": "周杰伦",
            "url": "https://qiniu.hwater.site/setting/bandaotiehe.mp3",
            "cover": "https://qiniu.hwater.site/custom/1678515309575.jpg"
        },
        {
            "id": 3,
            "title": "花开忘忧",
            "author": "周深",
            "url": "https://qiniu.hwater.site/setting/huakaiwangyou.mp3",
            "cover": "https://qiniu.hwater.site/custom/1678515366673.jpg"
        },
        {
            "id": 4,
            "title": "若梦",
            "author": "周深",
            "url": "https://qiniu.hwater.site/setting/ruomeng-zhoushen.mp3",
            "cover": "https://qiniu.hwater.site/custom/1678515387634.jpg"
        },
        {
            "id": 5,
            "title": "借",
            "author": "毛不易",
            "url": "https://qiniu.hwater.site/setting/maobuyi-jie.mp3",
            "cover": "https://qiniu.hwater.site/custom/1678515378304.jpg"
        },
    ];
    music_state.audio = document.getElementById('music_audio');
})

function toggleMusic() {
    music_wrapper_show.value = 1 - music_wrapper_show.value;
}
function showPlayerButton() {
    let player_play = document.getElementById('player_play');
    let player_pause = document.getElementById('player_pause');
    player_play.classList.remove("hide");
    player_pause.classList.add("hide");
}
function showPauseButton() {
    let player_play = document.getElementById('player_play');
    let player_pause = document.getElementById('player_pause');
    player_play.classList.add("hide");
    player_pause.classList.remove("hide");
}

function playCurrentMusic(id) {
    showPauseButton();
    let buttonAudio = music_state.audio;
    let src = buttonAudio.getAttribute("src");
    if (src == "" || src == null || id !== undefined) {
        if (id !== undefined) music_state.cur_id = id;
        let audio = music_state.list[music_state.cur_id].url;
        buttonAudio.setAttribute('src', audio);
    }
    buttonAudio.play();
    music_state.is_play = 1;
    music_state.is_paused = 0;
}

// 播放过程中调整进度条
function musicOnPlay() {
    let buttonAudio = music_state.audio;
    // let progress = document.getElementById('progress-' + music_state.cur_id);
    let width = buttonAudio.currentTime / buttonAudio.duration * 100;
    // progress.style.width = width + '%';
    music_state.cur_time = width;
}

function pauseCurrentMusic() {
    showPlayerButton();
    let buttonAudio = music_state.audio;
    buttonAudio.pause();
    music_state.is_play = 0;
    music_state.is_paused = 1;
}

function toggleMusicPlay() {
    music_state.is_play == 1 ? pauseCurrentMusic() : playCurrentMusic();
}

function music_player_goon() {
    playCurrentMusic();
}

function music_player_pause() {
    pauseCurrentMusic();
}

function music_player_play(id, event) {
    // 调整进度条
    if (id == music_state.cur_id && music_state.is_play) {
        let offsetX = event.offsetX;
        let width = event.target.offsetWidth;
        let buttonAudio = music_state.audio;
        let duration = buttonAudio.duration;
        let click_time = offsetX / width * duration;
        buttonAudio.currentTime = click_time;
        return;
    }
    playCurrentMusic(id);
}

function music_player_prev() {
    if (music_state.is_play) {
        pauseCurrentMusic();
    }
    let id = music_state.cur_id;
    let total = music_state.list.length;
    let new_id = (id == 0 ? total - 1 : id - 1);
    music_state.cur_time = 0.565508;
    playCurrentMusic(new_id);
}

function music_player_next() {
    if (music_state.is_play) {
        pauseCurrentMusic();
    }
    let id = music_state.cur_id;
    let total = music_state.list.length;
    let new_id = (id == total - 1 ? 0 : id + 1);
    music_state.cur_time = 0.565508;
    playCurrentMusic(new_id);
}

function audioEnded() {
    music_player_next();
}

</script>
<template>
    <div class="tool-button" data-tooltip-target="tooltip-music" data-tooltip-placement="left" @click="toggleMusic()">
        <font-awesome-icon :icon="['fas', 'headphones-simple']" v-if="!music_state.is_play" />
        <font-awesome-icon :icon="['fas', 'compact-disc']" v-else class="spin" />
    </div>
    <div id="tooltip-music" role="tooltip"
        class="whitespace-nowrap absolute z-10 invisible inline-block px-2 py-1 text-sm font-medium text-white bg-gray-500 rounded-md shadow-sm opacity-0 tooltip dark:bg-gray-700">
        音乐
        <div class="tooltip-arrow" data-popper-arrow></div>
    </div>

    <div class="music-player-wrapper">
        <audio src="" id="music_audio" @ended="audioEnded()" @timeupdate="musicOnPlay()"></audio>
        <div class="player-main mobile-show" :class="{ 'hide': music_wrapper_show === 0 }">
            <div class="music-player__disc">
                <div class="music-player__image" :class="{ 'play': music_state.is_play }">
                    <img alt="" :width="300" :src='Object.assign({}, music_state.list[music_state.cur_id]).cover' />
                </div>
                <div class="music-player__pointer" :class="{ 'play': music_state.is_play }">
                    <img src="../../assets/images/cd_tou.png" alt="" :width="300" />
                </div>
            </div>
            <div class="player">
                <div class="music__info">
                    <div class="music__info--title">{{ Object.assign({}, music_state.list[music_state.cur_id]).title }}
                    </div>
                    <div class="music__info--singer"> {{ Object.assign({}, music_state.list[music_state.cur_id]).author }}
                    </div>
                </div>
                <div class="flex items-center">
                    <svg t="1595172288959" class="icon" @click="this.playlist_toggle()" viewBox="0 0 1024 1024"
                        version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="1231" width="128" height="128">
                        <path
                            d="M864 416h-32V288c0-52.8-43.2-96-96-96H288C235.2 192 192 235.2 192 288v448c0 52.8 43.2 96 96 96h448c52.8 0 96-43.2 96-96v-128h32c17.6 0 32-14.4 32-32v-128c0-17.6-14.4-32-32-32z m-192 208a80 80 0 0 1-160 0 80 80 0 0 1 96-78.4V384h-128v240a80 80 0 0 1-160 0 80 80 0 0 1 96-78.4V352c0-17.6 14.4-32 32-32h192c17.6 0 32 14.4 32 32v272z"
                            fill="#364F6B" p-id="1232"></path>
                        <path
                            d="M620.16 128h65.28l-14.08-70.4c-2.88-14.72-16-25.6-31.36-25.6h-256c-15.36 0-28.48 10.88-31.36 25.6L338.56 128h281.6zM403.84 896h-65.28l14.08 70.4c2.88 14.72 16 25.6 31.36 25.6h256c15.36 0 28.48-10.88 31.36-25.6l14.08-70.4h-281.6z"
                            fill="#58cfff" p-id="1233" data-spm-anchor-id="a313x.7781069.0.i1" class="selected"></path>
                    </svg>
                    <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="76"
                        height="34" viewBox="0 0 76 34" class="">
                        <defs>
                            <linearGradient id="linear-gradient" x1="38" x2="38" y2="34" gradientUnits="userSpaceOnUse">
                                <stop offset="0" stop-color="#cad1db"></stop>
                                <stop offset="1" stop-color="#eff2f7"></stop>
                            </linearGradient>
                            <filter id="filter" x="0" y="0" width="76" height="34" filterUnits="userSpaceOnUse">
                                <feGaussianBlur result="blur" in="SourceAlpha"></feGaussianBlur>
                                <feFlood result="flood" flood-color="#1f2d3d" flood-opacity="0.1"></feFlood>
                                <feComposite result="composite" operator="out" in2="blur"></feComposite>
                                <feOffset result="offset" dy="1"></feOffset>
                                <feComposite result="composite-2" operator="in" in2="SourceAlpha"></feComposite>
                                <feBlend result="blend" in2="SourceGraphic"></feBlend>
                            </filter>
                            <linearGradient id="linear-gradient-2" x1="38" y1="31" x2="38" y2="3"
                                gradientUnits="userSpaceOnUse">
                                <stop offset="0" stop-color="#d8e0ea"></stop>
                                <stop offset="1" stop-color="#fff"></stop>
                            </linearGradient>
                            <filter id="filter-2" x="22" y="2" width="32" height="32" filterUnits="userSpaceOnUse">
                                <feOffset result="offset" dy="1" in="SourceAlpha"></feOffset>
                                <feGaussianBlur result="blur" stdDeviation="1"></feGaussianBlur>
                                <feFlood result="flood" flood-color="#1f2d3d" flood-opacity="0.28"></feFlood>
                                <feComposite result="composite" operator="in" in2="blur"></feComposite>
                                <feBlend result="blend" in="SourceGraphic"></feBlend>
                                <feGaussianBlur result="blur-2" in="SourceAlpha"></feGaussianBlur>
                                <feFlood result="flood-2" flood-color="#fff" flood-opacity="0.4"></feFlood>
                                <feComposite result="composite-2" operator="out" in2="blur-2"></feComposite>
                                <feOffset result="offset-2" dy="-1"></feOffset>
                                <feComposite result="composite-3" operator="in" in2="SourceAlpha"></feComposite>
                                <feBlend result="blend-2" in2="blend"></feBlend>
                            </filter>
                            <linearGradient id="linear-gradient-3" x1="11" y1="25" x2="11" y2="9"
                                xlink:href="#linear-gradient-2">
                            </linearGradient>
                            <filter id="filter-3" x="1" y="8" width="20" height="20" filterUnits="userSpaceOnUse">
                                <feOffset result="offset" dy="1" in="SourceAlpha"></feOffset>
                                <feGaussianBlur result="blur" stdDeviation="1"></feGaussianBlur>
                                <feFlood result="flood" flood-color="#1f2d3d" flood-opacity="0.28"></feFlood>
                                <feComposite result="composite" operator="in" in2="blur"></feComposite>
                                <feBlend result="blend" in="SourceGraphic"></feBlend>
                                <feGaussianBlur result="blur-2" in="SourceAlpha"></feGaussianBlur>
                                <feFlood result="flood-2" flood-color="#fff" flood-opacity="0.4"></feFlood>
                                <feComposite result="composite-2" operator="out" in2="blur-2"></feComposite>
                                <feOffset result="offset-2" dy="-1"></feOffset>
                                <feComposite result="composite-3" operator="in" in2="SourceAlpha"></feComposite>
                                <feBlend result="blend-2" in2="blend"></feBlend>
                            </filter>
                            <linearGradient id="linear-gradient-4" x1="65" y1="25" x2="65" y2="9"
                                xlink:href="#linear-gradient-2">
                            </linearGradient>
                            <filter id="filter-4" x="55" y="8" width="20" height="20" filterUnits="userSpaceOnUse">
                                <feOffset result="offset" dy="1" in="SourceAlpha"></feOffset>
                                <feGaussianBlur result="blur" stdDeviation="1"></feGaussianBlur>
                                <feFlood result="flood" flood-color="#1f2d3d" flood-opacity="0.28"></feFlood>
                                <feComposite result="composite" operator="in" in2="blur"></feComposite>
                                <feBlend result="blend" in="SourceGraphic"></feBlend>
                                <feGaussianBlur result="blur-2" in="SourceAlpha"></feGaussianBlur>
                                <feFlood result="flood-2" flood-color="#fff" flood-opacity="0.4"></feFlood>
                                <feComposite result="composite-2" operator="out" in2="blur-2"></feComposite>
                                <feOffset result="offset-2" dy="-1"></feOffset>
                                <feComposite result="composite-3" operator="in" in2="SourceAlpha"></feComposite>
                                <feBlend result="blend-2" in2="blend"></feBlend>
                            </filter>
                            <linearGradient id="linear-gradient-5" x1="39.5" y1="23" x2="39.5" y2="11"
                                gradientUnits="userSpaceOnUse">
                                <stop offset="0" stop-color="#657487"></stop>
                                <stop offset="0.5" stop-color="#738192"></stop>
                                <stop offset="0.51" stop-color="#8492a6"></stop>
                                <stop offset="1" stop-color="#99a9bf"></stop>
                            </linearGradient>
                            <filter id="filter-5" x="33" y="11" width="12" height="13" filterUnits="userSpaceOnUse">
                                <feOffset result="offset" dy="1" in="SourceAlpha"></feOffset>
                                <feGaussianBlur result="blur"></feGaussianBlur>
                                <feFlood result="flood" flood-color="#fff"></feFlood>
                                <feComposite result="composite" operator="in" in2="blur"></feComposite>
                                <feBlend result="blend" in="SourceGraphic"></feBlend>
                            </filter>
                            <linearGradient id="linear-gradient-6" x1="38.5" x2="38.5" xlink:href="#linear-gradient-5">
                            </linearGradient>
                            <filter id="filter-6" x="33" y="11" width="10" height="13" filterUnits="userSpaceOnUse">
                                <feOffset result="offset" dy="1" in="SourceAlpha"></feOffset>
                                <feGaussianBlur result="blur"></feGaussianBlur>
                                <feFlood result="flood" flood-color="#fff"></feFlood>
                                <feComposite result="composite" operator="in" in2="blur"></feComposite>
                                <feBlend result="blend" in="SourceGraphic"></feBlend>
                            </filter>
                            <filter id="filter-7" x="63" y="14" width="5" height="7" filterUnits="userSpaceOnUse">
                                <feOffset result="offset" dy="1" in="SourceAlpha"></feOffset>
                                <feGaussianBlur result="blur"></feGaussianBlur>
                                <feFlood result="flood" flood-color="#fff"></feFlood>
                                <feComposite result="composite" operator="in" in2="blur"></feComposite>
                                <feBlend result="blend" in="SourceGraphic"></feBlend>
                            </filter>
                            <filter id="filter-8" x="8" y="14" width="5" height="7" filterUnits="userSpaceOnUse">
                                <feOffset result="offset" dy="1" in="SourceAlpha"></feOffset>
                                <feGaussianBlur result="blur"></feGaussianBlur>
                                <feFlood result="flood" flood-color="#fff"></feFlood>
                                <feComposite result="composite" operator="in" in2="blur"></feComposite>
                                <feBlend result="blend" in="SourceGraphic"></feBlend>
                            </filter>
                        </defs>
                        <path id="bg" class="cls-1"
                            d="M65,28a10.943,10.943,0,0,1-8.17-3.654c-1.613-.143-3.711,1.484-5.318,2.948a16.97,16.97,0,0,1-27.087-.078c-1.579-1.448-3.614-3.031-5.2-2.932a11,11,0,1,1-.181-14.762c2.01,0.31,4.844-2.413,6.5-4.07A16.965,16.965,0,0,1,51.642,6.867c1.55,1.429,3.521,2.967,5.073,2.91A11,11,0,1,1,65,28Z">
                        </path>
                        <circle @click="toggleMusicPlay()" id="play-btn" class="cls-2" cx="38" cy="17" r="14"></circle>
                        <circle @click="music_player_prev()" id="prev-btn" class="cls-3" cx="11" cy="17" r="8"></circle>
                        <circle @click="music_player_next()" id="next-btn" class="cls-4" cx="65" cy="17" r="8"></circle>
                        <path @click="music_player_goon()" id="player_play" class="cls-5"
                            d="M40.993,21.5c-5.333,3-7,1.5-7-4.506s1.667-7.51,7-4.506S46.326,18.5,40.993,21.5Z"></path>
                        <path @click="music_player_pause()" id="player_pause" class="cls-6 hide"
                            d="M35.5,11A1.5,1.5,0,0,1,37,12.5v9a1.5,1.5,0,0,1-3,0v-9A1.5,1.5,0,0,1,35.5,11Zm6,0A1.5,1.5,0,0,1,43,12.5v9a1.5,1.5,0,0,1-3,0v-9A1.5,1.5,0,0,1,41.5,11Z">
                        </path>
                        <path @click="music_player_next()" id="next" class="cls-7"
                            d="M66.543,19.252C64.6,20.754,64,20,64,17s0.606-3.755,2.546-2.253S68.482,17.75,66.543,19.252Z">
                        </path>
                        <path @click="music_player_prev()" id="prev" class="cls-8"
                            d="M10.457,19.252C12.4,20.754,13,20,13,17s-0.606-3.755-2.545-2.253S8.518,17.75,10.457,19.252Z">
                        </path>
                    </svg>
                </div>
            </div>
            <div class="play-list-column" style="display: block">
                <ol class="play-list">
                    <li v-for="music in music_state.list" :key="music.id" :data-index="music.id" :class="{
                        'is-playing': music.id == music_state.cur_id && music_state.is_play,
                        'is-paused': music.id == music_state.cur_id && music_state.is_paused == 1
                    }" @click="music_player_play(music.id, $event)">
                        <div :id="'progress-' + music.id" class="music-progress cancel-click"
                            :style="{ 'width': music_state.cur_id == music.id ? music_state.cur_time + '%' : '0.565508%' }">
                        </div>
                        <span class="index cancel-click">{{ music.id + 1 }}</span>
                        <span class="title cancel-click">{{ music.title }}</span>
                        <span class="author cancel-click">{{ music.author }}</span>
                    </li>
                </ol>
            </div>
        </div>
    </div>
</template>
<style scoped>
.music-player-wrapper {
  position: relative;
  z-index: 3;
}

.music-player-wrapper .player-main {
  position: fixed;
  right: 50px;
  bottom: 168px;
  width: 300px;
  z-index: 10;
  -webkit-box-shadow: 0 15px 20px rgba(31, 45, 61, .2), 0 0 0 2px rgba(166, 172, 182, .5), 0 0 0 1px #fff inset;
  box-shadow: 0 15px 20px rgba(31, 45, 61, .2), 0 0 0 2px rgba(166, 172, 182, .5), 0 0 0 1px #fff inset;
  background: #f0f2f7;
  background: -webkit-gradient(linear, left top, left bottom, from(#f0f2f7), to(#fff));
  background: -webkit-linear-gradient(#f0f2f7, #fff);
  background: linear-gradient(#f0f2f7, #fff);
  padding: 10px 8px 0;
  border-radius: 6px;
  -webkit-transform-origin: calc(100% + 10px) calc(100% - 18px);
  transform-origin: calc(100% + 10px) calc(100% - 18px);
  -webkit-transition: .5s cubic-bezier(.5, 1.65, .5, 1);
  transition: .5s cubic-bezier(.5, 1.65, .5, 1);
  visibility: hidden;
  -webkit-transform: scale(0);
  transform: scale(0);
  opacity: 0
}

.dark .player-main {
  -webkit-box-shadow: 0 15px 20px rgba(31, 45, 61, .2), 0 0 0 1px rgba(166, 172, 182, .5), 0 0 0 1px #34425a inset;
  box-shadow: 0 15px 20px rgba(31, 45, 61, .2), 0 0 0 1px rgba(166, 172, 182, .5), 0 0 0 1px #34425a inset;
  background: #34425a;
  background: -webkit-gradient(linear, left top, left bottom, from(#34425a), to(#28374a));
  background: -webkit-linear-gradient(#34425a, #28374a);
  background: linear-gradient(#34425a, #28374a);
}

.music-player-wrapper .player-main:before {
  content: "";
  position: absolute;
  display: block;
  border-top: 5px solid transparent;
  border-bottom: 5px solid transparent;
  border-left: 5px solid #fefefe;
  bottom: 15px;
  right: -5px;
  z-index: 1
}

.music-player-wrapper .player-main:after {
  content: "";
  position: absolute;
  display: block;
  bottom: 14px;
  right: -7px;
  height: 5px;
  border-top: 6px solid transparent;
  border-bottom: 6px solid transparent;
  border-left: 6px solid rgba(31, 45, 61, .15)
}

.music-player-wrapper .player-main svg {
  opacity: 0;
  transition: .25s
}

.music-player-wrapper .player-main.mobile-show {
  visibility: visible;
  opacity: 1;
  -webkit-transform: scale(1);
  transform: scale(1)
}

.music-player-wrapper .player-main.mobile-show svg {
  opacity: 1
}

.music-player__disc {
  float: right;
  width: 90px;
  height: 90px;
  right: 15px;
  background: url(../../assets/images/disc.png) center no-repeat;
  background-size: 100%;
  position: relative
}

.music-player-wrapper .music-player__image {
  width: 55px;
  height: 55px;
  border-radius: 50%;
  position: absolute;
  overflow: hidden;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: auto
}

.music-player__pointer {
  width: 18px;
  position: absolute;
  right: -8px;
  top: 0;
  -webkit-transform-origin: right top;
  -ms-transform-origin: right top;
  transform-origin: right top;
  -webkit-transform: rotate(-15deg);
  -ms-transform: rotate(-15deg);
  transform: rotate(-15deg);
  -webkit-transition: all .3s;
  -o-transition: all .3s;
  transition: all .3s
}

.music-player-wrapper .player {
  position: relative;
  margin-bottom: 10px;
  min-height: 90px
}

.music__info {
  width: 100%;
  min-height: 49px;
  padding: 5px 0 0 10px;
  line-height: 1.6
}

.music__info .music__info--singer,
.music__info .music__info--title {
  width: 160px;
  white-space: nowrap;
  text-overflow: ellipsis;
  vertical-align: middle;
  position: relative;
  overflow: hidden;
  color: #6d7c8d
}

.music__info .music__info--title {
  font-size: 15px
}

.music__info .music__info--singer {
  color: #99a9bf;
  font-size: 13px
}

.music-player-wrapper .player .icon {
  width: 35px;
  height: 50px;
  cursor: pointer;
  vertical-align: middle
}

.music-player-wrapper .play-list {
  padding: 0;
  transition: .25s
}

.music-player-wrapper .play-list li {
  color: #8492a6;
  list-style: none;
  padding: 3px 5px 3px 3px;
  border-radius: 4px;
  cursor: pointer;
  white-space: nowrap;
  transition: .25s
}

.music-player-wrapper .play-list li:hover {
  background: rgba(32, 160, 255, .1)
}

.music-player-wrapper .play-list li .index {
  color: #99a9bf;
  display: inline-block;
  width: 15px;
  text-align: center;
  vertical-align: middle;
  margin-right: 3px;
  position: relative;
  z-index: 1
}

.music-player-wrapper .play-list li .title {
  display: inline-block;
  width: 145px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  vertical-align: middle;
  position: relative;
  z-index: 1
}

.music-player-wrapper .play-list li .author {
  display: inline-block;
  width: 80px;
  white-space: nowrap;
  overflow: hidden;
  float: right;
  text-align: right;
  text-overflow: ellipsis;
  vertical-align: middle;
  position: relative;
  z-index: 1
}

.music-player__image.play {
  -webkit-animation: disc 5s linear 0s infinite;
  animation: disc 5s linear 0s infinite
}

.music-player__pointer.play {
  -webkit-transform: rotate(0);
  -ms-transform: rotate(0);
  transform: rotate(0)
}

@-webkit-keyframes disc {
  from {
    -webkit-transform: rotate(0);
    transform: rotate(0)
  }

  to {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg)
  }
}

@keyframes disc {
  from {
    -webkit-transform: rotate(0);
    transform: rotate(0)
  }

  to {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg)
  }
}

.music-player-wrapper .play-list li.is-loading {
  color: #fff;
  background: linear-gradient(90deg, #20a0ff, #59ccff)
}

.music-player-wrapper .play-list li.is-loading .index {
  color: transparent;
  position: relative
}

.music-player-wrapper .play-list li.is-loading .index:before {
  content: "";
  display: block;
  position: absolute;
  top: 4px;
  right: 2px;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  border: 1px solid #fff;
  border-top-color: transparent;
  animation: rotate 1s linear infinite
}

.music-player-wrapper .play-list li.is-paused {
  color: #fff;
  background: linear-gradient(90deg, #20a0ff, #59ccff)
}

.music-player-wrapper .play-list li.is-paused .index {
  color: hsla(0, 0%, 100%, .6)
}

.music-player-wrapper .play-list li.is-playing {
  color: #fff;
  background: linear-gradient(90deg, #20a0ff, #59ccff);
  position: relative
}

.music-player-wrapper .play-list li.is-playing .index {
  color: transparent;
  background: url(../../assets/images/music_line.gif?h=3Fu24) 50% no-repeat
}

.music-player-wrapper .play-list li.is-playing .music-progress {
  content: "";
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  width: 0;
  background: rgba(31, 45, 61, .2);
  border-radius: 4px
}

.music-player-wrapper .play-list li.is-error {
  color: #ff4949
}

.music-player-wrapper .play-list li.is-error .index {
  color: transparent;
  background: url(../../assets/images/music_error.png?h=3pfsx) 50% no-repeat
}

@keyframes rotate {
  0% {
    transform: rotate(0)
  }

  to {
    transform: rotate(1turn)
  }
}

@media screen and (-webkit-min-device-pixel-ratio:2),
screen and (min-device-pixel-ratio:2) {
  .music-player-wrapper .play-list li.is-playing .index {
    background: url(../../assets/images/music_line@2x.gif) 50%/10px 10px no-repeat
  }

  .music-player-wrapper .play-list li.is-error .index {
    background: url(../../assets/images/music_error@2x.png?h=1P119) 50%/10px 10px no-repeat
  }
}

@media screen and (-webkit-min-device-pixel-ratio:3),
screen and (min-device-pixel-ratio:3) {
  .music-player-wrapper .play-list li.is-playing .index {
    background: url(../../assets/images/music_line@3x.gif?h=1Mwjs) 50%/10px 10px no-repeat
  }

  .music-player-wrapper .play-list li.is-error .index {
    background: url(../../assets/images/music_error@3x.png?h=3uFqG) 50%/10px 10px no-repeat
  }
}

@-webkit-keyframes aplayer-roll {
  0% {
    left: 0
  }

  to {
    left: -100%
  }
}

@keyframes aplayer-roll {
  0% {
    left: 0
  }

  to {
    left: -100%
  }
}

@-webkit-keyframes rotate {
  0% {
    -webkit-transform: rotate(0);
    transform: rotate(0)
  }

  to {
    -webkit-transform: rotate(1turn);
    transform: rotate(1turn)
  }
}

@keyframes rotate {
  0% {
    -webkit-transform: rotate(0);
    transform: rotate(0)
  }

  to {
    -webkit-transform: rotate(1turn);
    transform: rotate(1turn)
  }
}

.cls-1,
.cls-5,
.cls-6,
.cls-7,
.cls-8 {
  fill-rule: evenodd;
}

.cls-1 {
  fill: url(#linear-gradient);
  filter: url(#filter);
}

.cls-2 {
  fill: url(#linear-gradient-2);
  filter: url(#filter-2);
}

.cls-3 {
  fill: url(#linear-gradient-3);
  filter: url(#filter-3);
}

.cls-4 {
  fill: url(#linear-gradient-4);
  filter: url(#filter-4);
}

.cls-5 {
  fill: url(#linear-gradient-5);
  filter: url(#filter-5);
}

.cls-6 {
  fill: url(#linear-gradient-6);
  filter: url(#filter-6);
}

.cls-7,
.cls-8 {
  fill: #99a9bf;
}

.cls-7 {
  filter: url(#filter-7);
}

.cls-8 {
  filter: url(#filter-8);
}

.spin {
  -webkit-animation: fa-spin 2s infinite linear;
  animation: fa-spin 2s infinite linear;
}

.cancel-click {
  pointer-events: none;
}
</style>