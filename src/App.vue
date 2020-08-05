<template>
  <div id="app">
    <Header />
    <main class="row">

      <section class="player col-lg-6">
        <div class="song-cover"></div>
        <h2 class="song-title"><span>{{ current.artist }}</span> - {{ current.title }}</h2>
        <div class="controls">

          <a class="download" :href="current.src" download="" @click="player.loop = !player.loop">
            <i class="fa fa-download" aria-hidden="true"></i>
          </a>

          <button class="prev" @click="prev">
            <i class="fa fa-step-backward" aria-hidden="true"></i>
          </button>
          <button class="play" v-if="!isPlaying" @click="play">
            <i class="fa fa-play" aria-hidden="true"></i>
          </button>
          <button class="pause" v-else @click="pause">
            <i class="fa fa-pause" aria-hidden="true"></i>
          </button>
          <button class="next" @click="next">
            <i class="fa fa-step-forward" aria-hidden="true"></i>
          </button>

          <button class="repeat" @click="player.loop = !player.loop" :class="player.loop ? 'active' : ''">
            <i class="fa fa-retweet" aria-hidden="true"></i>
          </button>

        </div>

        <div class="volume">
          <i v-if="player.volume >= 0.5" class="fa fa-volume-up" aria-hidden="true" />
          <i v-else-if="player.volume <= 0.5" class="fa fa-volume-down" aria-hidden="true" />
          <i v-else-if="player.volume === 0" class="fa fa-volume-off" aria-hidden="true" />
          <input class="player-volume" @change="player.volume" v-model="player.volume" :min="0" :max="1" :step="0.05" type="range" width="30">
        </div>

      </section>

      <section class="playlist col-lg-6">
        <h2>Плэйлист</h2>
        <button v-for="song in songs" :key="song.src" @click="play(song)" :class="(song.src === current.src) ? 'song playing' : 'song'">
          {{ song.artist }} - {{ song.title }}
        </button>
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
    index: 3,
    isPlaying: false,
    songs: [
      {
        title: "Calm",
        artist: "DEPVRTXT",
        src: require('@/assets/songs/DEPVRTXT - Calm.mp3'),
      },
      {
        title: "OMAEVA",
        artist: "DEPVRTXT",
        src: require('@/assets/songs/DEPVRTXT - OMAEVA.mp3'),
      },
      {
        title: "No Title",
        artist: "DEPVRTXT",
        src: require('@/assets/songs/DEPVRTXT  - .mp3'),
      },
      {
        title: "Клеймо жертвы на рукаве",
        artist: "ccwa",
        src: require('@/assets/songs/ccwa - Клеймо жертвы на рукаве.mp3'),
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
        this.index++
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

  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;

  min-height: 75vh;
  margin-top: 6vh;

  .repeat{
    margin: 0 -15px;
    background: none;
    box-shadow: none;
    font-size: 22px;
    color: #787787;
    &.active{
      color: $dark;
    }
  }

  .volume{
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
    i{
      margin-right: 7px;
      font-size: 24px;
    }
  }
  .song-cover{
    //background: #232323;
    background: url("https://cdnb.artstation.com/p/assets/images/images/011/745/827/large/valeria-pulido-guts.jpg?1531196168");
    background-size: cover;
    background-position: bottom;
    background-repeat: no-repeat;
    width: 220px;
    height: 220px;
    border-radius: 15px;
    margin-bottom: 20px;
    box-shadow: 0 0 25px rgba($dark, 0.35);
  }
  .song-title{
    font-weight: 300;
    font-style: italic;
    color: #343434;
    margin-bottom: 15px;
    span{
      text-decoration: underline;
      font-style: normal;
      font-weight: 700;
    }
  }
  padding-top: 50px;
  .controls{
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .play, .pause{
    border-radius: 200px;
    font-size: 45px;
    width: 100px;
    height: 100px;
  }
  .pause{
    margin: 0;
    i{
      margin-bottom: 9px;
      margin-left: 2px;
    }
  }
  .play{
    margin: 0;
    i{
      margin-bottom: 7.5px;
      margin-left: 9px;
    }
  }
  .prev, .next{
    background: none;
    box-shadow: none;
    color: $dark;
    font-size: 40px;
    margin: 0;
  }
  .prev{

  }
  .download{
    display: block;
    color: $dark;
    margin: 0;
    font-size: 20px;
    padding: 9px 15px;
    padding-top: 15px;
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
    width: 70%;
    margin-top: 10px;
    transition: all 0.2s ease-in;
    @media (max-width: 768px) {
      width: 90%;
      margin-left: auto;
      margin-right: auto;
      padding: 20px 0;
      margin-bottom: 10px;
    }
    &.playing{
      background: none;
      outline: 2px solid $dark;
      transform: scale(1.05);
      color: $dark;
    }
  }
}
</style>
