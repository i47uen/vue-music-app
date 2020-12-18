<template>
  <div id="app">
    <Header />
    <main class="row">

      <section class="player col-lg-6">
        <div class="song-cover" :style="`backgroundImage: url(${current.cover})`">
        </div>
        <h2 class="song-title">
          <span>{{ current.artist }}</span> - {{ current.title }}
        </h2>
        <div class="controls">
          <a class="control download" :href="current.src" download="" @click="player.loop = !player.loop">
            <img :src="require('@/assets/images/icons/download.svg')" alt="download">
          </a>
          <div class="control prev" @click="prev">
            <img :src="require('@/assets/images/icons/prev.svg')" alt="<--">
          </div>
          <div class="control play" v-if="!isPlaying" @click="play">
            <img :src="require('@/assets/images/icons/play.svg')" alt="!>">
          </div>
          <div class="control pause" v-else @click="pause">
            <img :src="require('@/assets/images/icons/pause.svg')" alt="||">
          </div>
          <div class="control next" @click="next">
            <img :src="require('@/assets/images/icons/next.svg')" alt="-->">
          </div>
          <div class="control repeat" @click="toggleLoop" :class="isLooped ? 'active' : ''">
            <img :src="require('@/assets/images/icons/repeat.svg')" alt="r">
          </div>
        </div>

        <div class="volume">
          <img :src="require('@/assets/images/icons/volume.svg')" alt="volume">
          <input class="player-volume range" @change="player.volume" v-model="player.volume" :min="0" :max="1" :step="0.05" type="range" width="40">
        </div>
      </section>

      <section class="playlist col-lg-6">
        <h2 class="playlist-title">Плэйлист</h2>
        <div v-for="song in songs" :key="song.src" @click="play(song)" :class="(song.src === current.src) ? 'song playing' : 'song'">
          <div class="everlib-logo">
            <i class="everlib-logo-first-bar"></i>
            <i class="everlib-logo-second-bar"></i>
            <i class="everlib-logo-third-bar"></i>
          </div>
          <span>{{ song.artist }}</span> - {{ song.title }}
        </div>
      </section>

    </main>
  </div>
</template>

<script>
import Header from "@/components/Header"
export default {
  name: 'App',
  data: () => ({
    current: {},
    index: 0,
    isPlaying: false,
    isLooped: false,
    songs: [
      {
        title: "Happiness Does Not Wait (Jean Blanc Edit)",
        artist: "Ólafur Arnalds",
        src: require('@/assets/songs/Ólafur Arnalds - Happiness Does Not Wait (Jean Blanc Edit).mp3'),
        cover: "https://i1.sndcdn.com/artworks-000239661105-hbvulk-t500x500.jpg"
      },
    ],
    player: new Audio()
  }),

  methods: {
    play(song) {
      if (typeof song.src !== "undefined") {
        this.current = song;
        this.player.src = this.current.src;
      }
      this.player.play();
      this.isPlaying = true;
      this.player.addEventListener('ended', function () {
        if(!this.isLooped){
          this.index++
        }
        if (this.index > this.songs.length -1){
          this.index = 0;
        }

        this.current = this.songs[this.index];
        this.play(this.current);
      }.bind(this));
    },

    pause(){
      this.player.pause();
      this.isPlaying = false
    },

    prev(){
      this.index--
      if (this.index < 0){
        this.index = this.songs.length - 1;
      }
      this.current = this.songs[this.index];
      this.play(this.current);

      this.player.addEventListener('loadedmetadata', function (){
        this.current.duration = this.player.duration
      })

    },

    next(){
      this.index++
      if (this.index > this.songs.length -1){
        this.index = 0;
      }
      this.current = this.songs[this.index];
      this.play(this.current);
    },

    toggleLoop(){
      this.player.loop = !this.player.loop
      this.isLooped = !this.isLooped
    },

    convertTime (time) {
      let mins = Math.floor(time / 60);
      if (mins < 10) {
        mins = '0' + String(mins);
      }
      let secs = Math.floor(time % 60);
      if (secs < 10) {
        secs = '0' + String(secs);
      }
      return mins + ':' + secs;
    },
  },
  components: {
    Header
  },
  created() {
    this.current = this.songs[this.index];
    this.player.src = this.current.src;
  },
}
</script>





<style lang="scss">
@import "assets/scss/main";
  .player{
    color: $white;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;

    min-height: 75vh;
    margin-top: 11vh;

    .controls{
      display: flex;
      justify-content: center;
      align-items: center;
      .control{
      padding: 14px;
      cursor: pointer;
        img{
          height: 30px;
          width: auto;
        }
        filter: invert(1);
        &.download, &.repeat{
          img{
            height: 22px;
          }
        }
        &.prev, &.next{
          img{
            height: 38px;
          }
        }
        &.play{
          display: block;
          background: $white;
          border-radius: 80px;
          img{
            margin: 18px;
            height: 50px;
            transform: translateX(5px);
          }
        }
        &.pause{
          display: block;
          background: $white;
          border-radius: 80px;
          img{
            margin: 18px;
            height: 50px;
          }
        }
      }
    }
  }

  .volume{
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
    img{
      margin-right: 7px;
      height: 26px;
      filter: invert(1);
    }
  }

  .song-cover{
    background-size: cover;
    background-position: bottom;
    background-repeat: no-repeat;
    width: 220px;
    height: 220px;
    border-radius: 15px;
    margin-bottom: 30px;
    border: 1px solid #fff;
    box-shadow: 0 4px 25px rgba($white, 0.1);
  }

  .song-title{
    max-width: 460px;
    font-weight: 300;
    font-style: italic;
    color: $white;
    margin-bottom: 15px;
    span{
      text-decoration: underline;
      font-style: normal;
      font-weight: 700;
    }
  }

  .playlist{
    max-height: 75vh;
    overflow: auto;

    padding-top: 50px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;

    @media (max-width: 768px) {
      max-height: 100%;
      width: 100%;
      margin-bottom: 50px;
      padding-top: 0;
      padding-left: 35px;
    }

    .song{
      width: 80%;
      margin-top: 12px;
      padding: 10px 0;
      transition: all 0.2s ease-in;
      @media (max-width: 768px) {
        width: 90%;
        margin-left: auto;
        margin-right: auto;
        padding: 20px 0;
        margin-bottom: 10px;
      }
      span{
        font-weight: 600;
      }
      &.playing{
        background: none;
        outline: 2px solid $white;
        transform: scale(1.02);
        color: $white;
      }
    }
  }
</style>
