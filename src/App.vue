<template>
  <v-app id="inspire">
    <v-app-bar app color="primary" dark >
      <v-toolbar-title>Sherpa Shootout</v-toolbar-title>
    </v-app-bar>

    <v-main>
      <v-container fluid>
        <v-row align="start" justify="center">
          <v-col>
          <home></home>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
    <v-footer color="primary" app>
      <span class="white--text">&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
import Home from "@/views/Home";

export default {
  components: {
    Home,

  },
  props: {
    source: String,
  },
  methods: {

  },
  data() {
    return {
      contentRendered: false,
      progress: 0,
      generatingPdf: false,
      pdfDownloaded: false,
    };
  },
}
</script>