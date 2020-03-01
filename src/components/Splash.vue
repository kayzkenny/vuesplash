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
    <v-container>
      <v-card v-if="search" class="my-4 pa-4">Search results for "{{ search }}"</v-card>
      <v-row v-if="isLoading">
        <v-col cols="3" v-for="n in 6" :key="n">
          <v-sheet
            :color="`grey ${theme.isDark ? 'darken-2' : 'lighten-4'}`"
            class="px-3 pt-3 pb-3"
          >
            <v-skeleton-loader class="mx-auto" max-width="300" type="card"></v-skeleton-loader>
          </v-sheet>
        </v-col>
      </v-row>
      <v-row v-if="!isLoading">
        <v-col cols="3" v-for="splash in unsplash" :key="splash.id" align-self="center">
          <v-card max-width="360" @click="showDialog(splash)">
            <v-img
              class="white--text align-end"
              :height="360"
              gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
              :src="splash.urls.small"
            >
              <span class="white--text title font-weight-light pl-4">{{ splash.user.name }}</span>
              <p class="white--text subtitle-2 font-weight-light pl-4">{{ splash.user.location }}</p>
            </v-img>
          </v-card>
        </v-col>
      </v-row>
      <v-row justify="center" v-if="!isLoading">
        <v-col cols="8">
          <v-container class="max-width">
            <v-pagination
              v-model="page"
              class="my-4"
              :length="total_pages"
              @input="input"
              @next="next"
              @previous="prev"
            ></v-pagination>
          </v-container>
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
    unsplash: [],
    isLoading: true,
    search: "",
    endpoint: "https://api.unsplash.com/search/photos",
    client_id: "N8SLkZagem7GWVCHDbRDJHXBIusgk9-w0EHS2t5hADE",
    page: 1,
    total: 10,
    total_pages: 1,
    defaultSearch: "africa",
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
    /**
     * Show the dialog and set its data
     * @function
     * @param {object} splash - the selected picture
     */
    showDialog(splash) {
      this.dialogSplash = splash;
      this.dialog = true;
    },
    /**
     * Fetch photos with search string and assign to unsplash property
     * @function
     */
    searchQuery() {
      this.isLoading = true;
      fetch(
        `${this.endpoint}?page=${this.page}&query=${this.search}&client_id=${this.client_id}`
      )
        .then(response => {
          this.searchPlaceholder = `Search Results for "${this.search}"`;
          this.isLoading = false;
          return response.json();
        })
        .then(data => {
          this.unsplash = data.results;
          this.total = data.total;
          this.total_pages = data.total_pages;
        });
    },
    /**
     * Fetch photos with default search string and assign to unsplash property
     * @function
     */
    defaultSearchQuery() {
      fetch(
        `${this.endpoint}?page=${this.page}&query=${this.defaultSearch}&client_id=${this.client_id}`
      )
        .then(response => {
          this.isLoading = false;
          return response.json();
        })
        .then(data => {
          this.unsplash = data.results;
          this.total = data.total;
          this.total_pages = data.total_pages;
        });
    },
    /**
     * If search input is null run searchQuery else run default search
     * @function
     */
    paginate() {
      this.search ? this.searchQuery() : this.defaultSearchQuery();
    },
    /**
     * Called when the next button is clicked on v-pagination
     * @function
     */
    next() {
      this.paginate();
    },
    /**
     * Called when the previous button is clicked on v-pagination
     * @function
     */
    prev() {
      this.paginate();
    },
    /**
     * Called when the input button is clicked on v-pagination
     * @function
     */
    input() {
      this.paginate();
    }
  },
  created() {
    this.defaultSearchQuery();
  }
};
</script>
