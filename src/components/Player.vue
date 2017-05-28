<template>
  <div class="c-player">
    <div class="c-player__current">
      <img v-if="artistImage" :src="artistImage" class="c-player__artist-image" />
      <img v-else :src="placeholder" class="c-player__artist-image" />

      <div class="c-player__controls" v-if="track">
        <input type="range" v-model="trackTime">
        <button @click="pauseTrack" class="c-player__pause" v-show="playing">
          <span class="u-show-for-sr">Pause</span>
        </button>
        <button @click="playTrack" class="c-player__play" v-show="!playing">
          <span class="u-show-for-sr">Play</span>
        </button>
      </div>
    </div>

    <div class="c-player__playlist-container">

      <transition-group name="list" tag="ul" class="c-player__playlist">
        <li
          :key="album.id"
          class="c-player__playlist-item"
          v-for="album in albums"
          @click="handleClick(album)">
          <div class="c-player__album-preview">
            <img :src="album.images[0].url" :alt="album.name">
          </div>
          <h3 class="c-player__playlist-item-title">{{ album.name }}</h3>
        </li>
      </transition-group>

    </div>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    props: ['albums', 'placeholder'],

    data() {
      return {
        artistImage: '',
        track: null,
        playing: false,
        trackTime: 0
      }
    },

    methods: {
      audioTimeUpdated() {
        let amountPlayed = (this.track.currentTime / this.track.duration) * 100;

        this.trackTime = amountPlayed;
      },

      addListeners() {
        this.track.addEventListener(
          'timeupdate',
          this.audioTimeUpdated,
          false
        );
      },

      handleClick(item) {
        let phoneScreen = document.querySelector('.c-phone__screen');

        phoneScreen.scrollTop = 0;

        this.artistImage = item.images[0].url;

        this.fetchTrack(item.id);
      },

      fetchTrack(id) {
        axios.get(`https://api.spotify.com/v1/albums/${id}`).then(response => {
          if (this.track) {
            this.track.pause();
          }

          let audioObject = new Audio(response.data.tracks.items[0].preview_url);

          audioObject.play();

          this.playing = true;

          this.track = audioObject;

          this.addListeners();
        });
      },

      pauseTrack() {
        if (this.track) {
          this.track.pause();
          this.playing = false;
        }
      },

      playTrack() {
        if (this.track) {
          this.track.play();
          this.playing = true;
        }
      }
    },

    ready() {
      this.$on('showResults', (results) => {
        this.albums = results;
        this.artistImage = results[0].images[0].url;

        this.pauseTrack();
        this.track = null;
      });
    }
  };
</script>

<style media="screen">
.list-enter-active, .list-leave-active {
  transition: all 0.4s;
}
.list-enter, .list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
</style>
