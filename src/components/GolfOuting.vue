<template>

  <v-container fluid>
    <v-row class="text-center">
      <v-col class="mb-0">
        <p class="display-2 font-weight-bold mb-0">
          Sherpa Shootout {{ new Date().getFullYear() }}
        </p>
      </v-col>
    </v-row>
    <v-form ref="golferForm" v-model="valid" lazy-validation>
    <v-row align="center">
        <v-text-field ref="golferName" class="mr-15" v-model="golfer.name" label="Golfer" :rules="golferRules" required></v-text-field>
        <v-text-field type="number" min="1" class="mr-15 score" v-model="golfer.score" label="Score" :rules="scoreRules" required></v-text-field>
        <v-btn color="primary" v-on:click="addGolfer()" :disabled="!valid">Add Golfer</v-btn>
    </v-row>
    <v-row align="center">
      <p><v-btn color="secondary" v-if="golfers.length > 0" v-on:click="clearGolfers()">Clear Golfer Data</v-btn></p>
    </v-row>
    </v-form>
    <v-expansion-panels multiple v-if="golfers.length > 0" v-model="panel">
      <v-expansion-panel>
        <v-expansion-panel-header>Golfers</v-expansion-panel-header>
        <v-expansion-panel-content>
          <v-row justify="space-around">
            <v-col cols="2"  v-for="(golfer, index ) in golfers" v-bind:key="index" justify="center">
              <div><v-icon color="error" v-on:click="deleteGolfer( index )">fa-trash</v-icon> <b> {{ golfer.name }}</b></div>
              <div class="pl-6">{{ golfer.score}}</div>
            </v-col>
          </v-row>
          <v-row>
            <v-btn class="primary" v-on:click="generateTeams()" v-if="golfers.length > 1" >Choose Teams</v-btn>
          </v-row>
        </v-expansion-panel-content>
      </v-expansion-panel>
      <v-expansion-panel>
        <v-expansion-panel-header>Teams</v-expansion-panel-header>
        <v-expansion-panel-content>
          <v-row class="mb-2">
            <v-btn class="primary" v-on:click="downloadTeams()" v-if="teams.length > 0" >Download PDF</v-btn>
          </v-row>
          <v-simple-table dense fixed-header height="400">
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
          <v-row align="center">
            <p><v-btn color="secondary" v-on:click="clearTeams()">Clear Team Data</v-btn></p>
          </v-row>
        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-expansion-panels>
    <vue-html2pdf
        :paginate-elements-by-height="pdfOpts.paginateByHeight"
        :filename="pdfOpts.fileName"
        :preview-modal="pdfOpts.previewModal"
        ref="html2Pdf"
    >
      <pdf-content slot="pdf-content" :teams="teams" />
    </vue-html2pdf>
  </v-container>

</template>
<style>
.score input[type='number'] {
  -moz-appearance:textfield;
}
.score input::-webkit-outer-spin-button,
.score input::-webkit-inner-spin-button {
  -webkit-appearance: none;
}
</style>
<script>
import VueHtml2pdf from "vue-html2pdf";
import PdfContent from "@/components/PdfContent";
  export default {
  components:{
    VueHtml2pdf,
    PdfContent,
  }
  ,
    name: 'GolfOuting',
    teamsChosen: false,

    data: () => ({
     golferRules:[
      value => !!value || 'Name is required.'
     ],
     scoreRules:[
       value => !!value || 'Score is required.',
       value => ( !isNaN( value ) && value == parseInt( value ) ) || 'Score must be a a whole number greater than 0'
     ],
     valid: true,
      pdfOpts:{
        paginateByHeight: 1400,
        fileName: 'Sherpa Shootout ' + new Date().getFullYear(),
        previewModal: false
      },
      golfers:[
        {
        name: 'Kubic, Tom',
        score: 65
        },
        {
        name: 'Stroz, Scott',
        score: 70
        }
      ],
      teams: [

      ],
      golfer: {},
      panel: [ 0, 1 ]
    }),
    methods:{
      addGolfer: function(){
        var isValid = this.$refs.golferForm.validate();
        if( isValid ){
          this.golfers.push( { id: this.getId(), name: this.golfer.name, score: this.golfer.score } );
          this.golfers.sort( function( a ,b ){
            if (a.name < b.name)
              return -1;
            if (a.name > b.name)
              return 1;
            return 0;
          })
          this.golfer = {};
          this.$refs.golferName.focus();
          this.clearTeams();
        }

      },
      deleteGolfer: function( idx ){
        this.golfers.splice( idx, 1 );
        this.clearTeams();
      },
      clearGolfers: function(){
        this.golfers = [];
        this.clearTeams();
      },
      clearTeams: function(){
        this.teams = [];
      },
      generateTeams: function(){
        this.teams = []
        var golfers = this.golfers.map(o => ({...o}))

        golfers.sort = function(){
          return this.random( -1, 1 );
        }

        for( var g=0; g <Math.ceil( this.golfers.length/2 ); g++ ){
          var team = [];
          //Get first golfer for team
          var g1Idx = this.random( 0, golfers.length - 1 );
          team.push( golfers[ g1Idx ] );
          golfers.splice( g1Idx, 1 );
          golfers.sort = function(){
            return this.random( -1, 1 );
          }
          // Get second golfer. If array is empty, we have odd number of golfers so grab random row from source
          // and make sure it is not the same golfer
          if( golfers.length > 0 ){
            var g2Idx =  this.random( 0, golfers.length - 1 );
            team.push( golfers[ g2Idx ] )
            golfers.splice( g2Idx, 1 );
            golfers.sort = function(){
              return this.random( -1, 1 );
            }
          }
          else{
            var validPartner = false;
            while ( !validPartner ){
              var idx = this.random( 0, this.golfers.length );
              if( this.golfers[ idx ].id != team[ 0 ].id ){
                team.push( { name: '* ' + this.golfers[ idx ].name, score: this.golfers[ idx ].score } )
                validPartner = true;
              }
            }
          }
          team.sort( function( a ,b ){
            if (a.name < b.name)
              return -1;
            if (a.name > b.name)
              return 1;
            return 0;
          })
          this.teams.push({ name: team[0].name + ' ( ' + parseInt(team[0].score) +' )/' + team[1].name + ' ( ' + parseInt(team[1].score) +' )', score: parseInt(team[0].score) + parseInt( team[1].score) })
        }
        this.teams.sort( function( a ,b ){
          if (a.score < b.score)
            return -1;
          if (a.score > b.score)
            return 1;
          return 0;
        })
        this.teamsChosen = true
      },
      random: function( min, max ){
        return Math.floor(Math.random() * (max - min + 1)) + min;
      },
      getId: function(){
        return Math.random().toString(36).substring(2, 15) + Math.random().toString(36).substring(2, 15);
      },
      downloadTeams: function(){
        this.$refs.html2Pdf.generatePdf();
      }
    },
  }
</script>
