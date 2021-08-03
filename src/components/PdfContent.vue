<template>
  <section class="pdf-content">
    <v-row class="text-center">
      <v-col class="mb-0">
        <h1 class="text-center">
          Sherpa Shootout {{ shootOutYear }}
        </h1>
      </v-col>
    </v-row>
    <v-simple-table dense>
      <template v-slot:default>
        <thead>
          <tr>
            <th style="width: 50px">Pos.</th>
            <th>Team</th>
            <th>Score</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(team, index) in teams" :key="index">
            <td>{{ index === 0 ? 1 : team.score === teams[ index -1 ].score ? '' : index + 1 }}</td>
            <td>{{ team.name }}</td>
            <td>{{ team.score }}</td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
  </section>
</template>

<script>
export default {
  props:{
    teams:{
      type: Array
    }
  },
  data() {
    return {
      shootOutYear: new Date().getFullYear()
    };
  },

  mounted() {
    this.$nextTick(() => {
      setTimeout(() => {
        this.$emit("domRendered");
      }, 1000);
    });
  },
};
</script>

<style lang="scss" scoped>
section{
  font-family: "Roboto", sans-serif;
}
.text-center{
  text-align: center;
}
</style>