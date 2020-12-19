<template>
  <div>
    <div v-if="isPlaying" class="eq">
      <i class="eq-first"></i>
      <i class="eq-second"></i>
      <i class="eq-third"></i>
    </div>
    <span>{{ artist }}</span> - {{ title }}
  </div>
</template>

<script>
export default {
  name: "Playlist",
  props: {
    artist: String,
    title: String,
    isPlaying: Boolean,
  },
};
</script>

<style lang="scss">
@import "../assets/scss/_variables.scss";
.playlist {
  display: block;
  max-height: 75vh;
  overflow: auto;

  padding-top: 30px;
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

  .song {
    text-align-last: left;
    width: 80%;
    margin-top: 12px;
    padding: 10px 0;
    padding-left: 15px;
    transition: all 0.2s ease-in;
    @media (max-width: 768px) {
      width: 100%;
      margin-left: auto;
      margin-right: auto;
      padding: 20px 0;
      margin-bottom: 10px;
    }
    span {
      font-weight: 600;
    }
    &.playing {
      background: none;
      outline: 2px solid $white;
      transform: scale(1.02);
      color: $white;
    }
  }
}
</style>
