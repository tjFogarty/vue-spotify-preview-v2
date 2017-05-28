<template>
  <div class="c-phone">
    <div class="c-phone__screen">
      <search @foundAlbums="setAlbums"></search>
      <player :albums="albums" :placeholder="placeholderArtistImage"></player>
    </div>
  </div>
</template>

<script>
  import Player from './components/Player.vue';
  import Search from './components/Search.vue';

  export default {
    data() {
      return {
        albums: [],
        placeholderArtistImage: null
      };
    },

    components: {
      Player,
      Search
    },

    methods: {
      setAlbums(albums) {
        this.albums = albums;
        this.placeholderArtistImage = albums[0].images[0].url;
      }
    }
  };
</script>
