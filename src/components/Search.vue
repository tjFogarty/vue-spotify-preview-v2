<template>
  <div class="c-search-container">
    <form class="c-search" @submit.prevent="onSubmit">
      <input type="search" placeholder="Search for an artist..." class="c-search__input" v-model="searchTerm">
      <button type="submit" class="c-search__submit">Search</button>
    </form>

    <p v-if="showError && searchTerm.length" class="c-search__message">{{ searchTerm }} not found</p>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    data() {
      return {
        searchTerm: 'Thrice',
        showError: false
      }
    },

    mounted() {
      this.performSearch();
    },

    methods: {
      onSubmit() {
        this.performSearch();
      },

      performSearch() {
        if (!this.searchTerm) {
          return;
        }

        axios.get(`https://api.spotify.com/v1/search?type=album&q=${this.searchTerm}`)
          .then(response => {
            if (response.status === 200 && response.data.albums.total) {
              this.showError = false;
              this.$emit('foundAlbums', response.data.albums.items);
            } else {
              this.showError = true;
            }
          }).catch(err => {
            this.showError = true;
          });
      }
    }
  };
</script>
