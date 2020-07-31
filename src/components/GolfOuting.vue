<template>
  <v-form>
  <v-container>
    <v-row class="text-center">
      <v-col class="mb-0">
        <p class="display-2 font-weight-bold mb-0">
          Golf Outing Info
        </p>
      </v-col>
    </v-row>
    <v-row align="center">
        <v-text-field class="mr-15" v-model="golfer.name" label="Golfer" required ></v-text-field>
        <v-text-field class="mr-15" v-model="golfer.score" label="Score" required ></v-text-field>
        <v-btn color="primary" v-on:click="addGolfer()">Add Golfer</v-btn>
    </v-row>
    <v-row v-for="(golfer, index) in golfers" v-bind:key="index">
      <v-col>{{golfer.name}}</v-col>
      <v-col>{{golfer.score}}</v-col>
      <v-btn color="error"><v-icon>fa-trash</v-icon></v-btn>
    </v-row>
  </v-container>
  </v-form>
</template>

<script>
  export default {
    name: 'GolfOuting',

    data: () => ({
      golfers:[],
      golfer: {}
    }),
    methods:{
      addGolfer: function(){
        this.golfers.push( { name: this.golfer.name, score: this.golfer.score } );
        this.golfer = {}
      }
    }
  }
</script>
