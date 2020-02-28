<template>
  <div>
    <v-toolbar class="px-4">
      <v-toolbar-title>Unsplash Demo</v-toolbar-title>
      <v-form @submit.prevent="searchQuery">
        <v-text-field
          class="mx-4"
          flat
          full-width
          hide-details
          placeholder="search"
          prepend-inner-icon="mdi-magnify"
          solo
          validate-on-blur
          @change="searchQuery"
          v-model="search"
        ></v-text-field>
      </v-form>
    </v-toolbar>
    <!-- <v-progress-linear v-if="isLoading" indeterminate color="green"></v-progress-linear> -->
    <v-container>
      <v-card v-if="search" class="my-4 pa-4">Search Results for "{{ search }}"</v-card>
      <v-row v-if="isLoading">
        <v-col cols="4" v-for="n in 6" :key="n">
          <v-sheet
            :color="`grey ${theme.isDark ? 'darken-2' : 'lighten-4'}`"
            class="px-3 pt-3 pb-3"
          >
            <v-skeleton-loader class="mx-auto" max-width="300" type="card"></v-skeleton-loader>
          </v-sheet>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="4" v-for="splash in unsplash" :key="splash.id" align-self="center">
          <v-card max-width="360" @click="showDialog(splash)">
            <v-img
              class="white--text align-end"
              :height="450"
              gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
              :src="splash.urls.small"
            >
              <span class="white--text title font-weight-light pl-4">{{ splash.user.name }}</span>
              <p class="white--text subtitle-2 font-weight-light pl-4">{{ splash.user.location }}</p>
            </v-img>
          </v-card>
        </v-col>
      </v-row>
      <v-dialog v-model="dialog" width="800">
        <v-card>
          <v-img
            class="white--text align-end"
            :height="450"
            gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
            :src="dialogSplash.urls.full"
            :lazy-src="dialogSplash.urls.small"
          >
            <template v-slot:placeholder>
              <v-row class="fill-height ma-0" align="center" justify="center">
                <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
              </v-row>
            </template>
          </v-img>
          <v-card-text class="mt-6">
            <span class="headline pl-4">{{ dialogSplash.user.name }}</span>
            <p class="subtitle-2 pl-4 font-weight-bold">{{ dialogSplash.user.location }}</p>
          </v-card-text>
        </v-card>
      </v-dialog>
    </v-container>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  inject: ["theme"],
  data: () => ({
    unsplash: null,
    isLoading: true,
    search: "",
    dialog: false,
    isActive: false,
    dialogSplash: {
      urls: {
        full: ""
      },
      user: {
        name: "",
        location: ""
      }
    }
  }),
  methods: {
    showDialog(splash) {
      this.dialogSplash = splash;
      this.dialog = true;
      // console.log(splash);
    },
    searchQuery() {
      this.isLoading = true;
      fetch(
        `https://api.unsplash.com/search/photos?page=1&query=${this.search}&client_id=N8SLkZagem7GWVCHDbRDJHXBIusgk9-w0EHS2t5hADE`
      )
        .then(response => {
          this.searchPlaceholder = `Search Results for "${this.search}"`;
          this.isLoading = false;
          return response.json();
        })
        .then(data => {
          this.unsplash = data.results;
          console.log(data);
        });
    }
  },
  created() {
    fetch(
      "https://api.unsplash.com/search/photos?page=1&query=africa&client_id=N8SLkZagem7GWVCHDbRDJHXBIusgk9-w0EHS2t5hADE"
    )
      .then(response => {
        this.isLoading = false;
        return response.json();
      })
      .then(data => {
        this.unsplash = data.results;
        console.log(data);
      });
  }
};
</script>
