<template>
  <div id="app">
    <Header />
    <main class="row">
      <section class="player col-lg-6">
        <div
          class="song-cover"
          :style="`backgroundImage: url(${current.cover})`"
        ></div>
        <h2 class="song-title">
          <span>{{ current.artist }}</span> - {{ current.title }}
        </h2>
        <div class="controls">
          <a
            class="control download"
            :href="current.src"
            download=""
            @click="player.loop = !player.loop"
          >
            <img
              :src="require('@/assets/images/icons/download.svg')"
              alt="download"
            />
          </a>
          <div class="control prev" @click="prev">
            <img :src="require('@/assets/images/icons/prev.svg')" alt="<-" />
          </div>
          <div class="control play" v-if="!isPlaying" @click="play">
            <img :src="require('@/assets/images/icons/play.svg')" alt="!>" />
          </div>
          <div class="control pause" v-else @click="pause">
            <img :src="require('@/assets/images/icons/pause.svg')" alt="||" />
          </div>
          <div class="control next" @click="next">
            <img :src="require('@/assets/images/icons/next.svg')" alt="->" />
          </div>
          <div
            class="control repeat"
            @click="toggleLoop"
            :class="isLooped ? 'active' : ''"
          >
            <img :src="require('@/assets/images/icons/repeat.svg')" alt="r" />
          </div>
        </div>

        <div class="volume">
          <img
            :src="require('@/assets/images/icons/volume.svg')"
            alt="volume"
          />
          <input
            class="player-volume range"
            @change="player.volume"
            v-model="player.volume"
            :min="0"
            :max="1"
            :step="0.05"
            type="range"
            width="40"
          />
        </div>
      </section>

      <section class="playlist col-lg-6">
        <h2 class="playlist-title">Playlist</h2>
        <Playlist
          v-for="song in songs"
          :key="song.src"
          :class="song.src === current.src ? 'song playing' : 'song'"
          :artist="song.artist"
          :title="song.title"
          :isPlaying="song.src === current.src"
          @click="play(song)"
        />
      </section>
    </main>
  </div>
</template>

<script>
import Header from "@/components/Header";
import Playlist from "@/components/Playlist";
export default {
  name: "App",
  data: () => ({
    current: {},
    index: 0,
    isPlaying: false,
    isLooped: false,
    songs: [
      {
        title: "Happiness Does Not Wait (Jean Blanc Edit)",
        artist: "Ólafur Arnalds",
        src: require("@/assets/songs/Ólafur Arnalds - Happiness Does Not Wait (Jean Blanc Edit).mp3"),
        cover:
          "https://i1.sndcdn.com/artworks-000239661105-hbvulk-t500x500.jpg",
      },
      {
        title: "Happy Nation",
        artist: "Fred & Mykos Radio Remix",
        src: require("@/assets/songs/Happy Nation (Fred & Mykos Radio Remix).mp3"),
        cover:
          "https://i1.sndcdn.com/artworks-000341514591-uom37y-t500x500.jpg",
      },
    ],
    player: new Audio(),
  }),

  methods: {
    play(song) {
      if (typeof song.src !== "undefined") {
        this.current = song;
        this.player.src = this.current.src;
      }
      this.player.play();
      this.isPlaying = true;
      this.player.addEventListener(
        "ended",
        function () {
          if (!this.isLooped) {
            this.index++;
          }
          if (this.index > this.songs.length - 1) {
            this.index = 0;
          }

          this.current = this.songs[this.index];
          this.play(this.current);
        }.bind(this)
      );
    },

    pause() {
      this.player.pause();
      this.isPlaying = false;
    },

    prev() {
      this.index--;
      if (this.index < 0) {
        this.index = this.songs.length - 1;
      }
      this.current = this.songs[this.index];
      this.play(this.current);

      this.player.addEventListener("loadedmetadata", function () {
        this.current.duration = this.player.duration;
      });
    },

    next() {
      this.index++;
      if (this.index > this.songs.length - 1) {
        this.index = 0;
      }
      this.current = this.songs[this.index];
      this.play(this.current);
    },

    toggleLoop() {
      this.player.loop = !this.player.loop;
      this.isLooped = !this.isLooped;
    },

    convertTime(time) {
      let mins = Math.floor(time / 60);
      if (mins < 10) {
        mins = "0" + String(mins);
      }
      let secs = Math.floor(time % 60);
      if (secs < 10) {
        secs = "0" + String(secs);
      }
      return mins + ":" + secs;
    },
  },
  components: {
    Header,
    Playlist,
  },
  created() {
    this.current = this.songs[this.index];
    this.player.src = this.current.src;
  },
};
</script>



<style lang="scss">
@import "assets/scss/main";
.player {
  color: $white;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;

  min-height: 75vh;
  margin-top: 3vh;

  .controls {
    margin-top: 3vh;
    display: flex;
    justify-content: center;
    align-items: center;
    .control {
      padding: 14px;
      cursor: pointer;
      img {
        height: 30px;
        width: auto;
      }
      filter: invert(1);
      &.download {
        img {
          height: 22px;
        }
      }
      &.repeat {
        &.active {
          img {
            filter: invert(0);
          }
        }
        img {
          height: 22px;
          filter: invert(0.5);
        }
      }
      &.prev,
      &.next {
        img {
          height: 38px;
        }
      }
      &.play {
        display: block;
        background: $white;
        border-radius: 80px;
        img {
          margin: 18px;
          height: 50px;
          transform: translateX(5px);
        }
      }
      &.pause {
        display: block;
        background: $white;
        border-radius: 80px;
        img {
          margin: 18px;
          height: 50px;
        }
      }
    }
  }
}

.volume {
  margin-top: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  img {
    margin-right: 7px;
    height: 26px;
    filter: invert(1);
  }
}

.song-cover {
  background-size: cover;
  background-position: bottom;
  background-repeat: no-repeat;
  width: 250px;
  height: 250px;
  border-radius: 15px;
  margin-bottom: 30px;
  border: 1px solid #fff;
  box-shadow: 0 4px 25px rgba($white, 0.1);
}

.song-title {
  max-width: 460px;
  font-weight: 300;
  font-style: italic;
  color: $white;
  margin-bottom: 15px;
  span {
    text-decoration: underline;
    font-style: normal;
    font-weight: 700;
  }
}
</style>
