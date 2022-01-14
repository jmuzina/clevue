<template>
  <div class="container-fluid" v-if="gotPlayerRoster == true" id="app">
    <div class="row">
      <div class="player-list col-md-6" v-if="gotPlayerRoster == true">
        <div
          class="player-container"
          v-for="(item, index) in playerRoster"
          v-bind:item="item"
          v-bind:index="index"
          v-bind:key="item.playerId"
        >
          <Player
            :playerId="item.playerId"
            @player-selected="player_selected($event)"
          />
        </div>
      </div>
      <div class="pitchData col-md-6">
        <div class="row">
          <Panel title="All Pitches">
            <PitchPlot :pitches="selectedPlayerData.pitchData" />
          </Panel>

          <Panel
            v-for="(item, index) in pitchTypes"
            v-bind:item="item"
            v-bind:index="index"
            v-bind:key="item.pitchType"
            :title="index"
          >
            <PitchPlot :pitches="Object.values(item)" />
          </Panel>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Player from "./components/Player";
import Panel from "./components/layout/Panel.vue";
import PitchPlot from "./components/plots/PitchPlot.vue";

export default {
  data() {
    return {
      playerRoster: this.getAllPlayers(),
      gotPlayerRoster: false,
      selectedPlayerData: {},
      selectedPitchType: "Fastball",
      sortAscending: true,
      sortKey: "fullName",

      pitchTypes: {},

      player_selected: function (selected) {
        this.selectedPlayerData = selected;
        this.pitchTypes = {};
        for (const k in this.selectedPlayerData.pitchData) {
          this.selectedPlayerData.pitchData[k].isVisible = true;
          this.selectedPlayerData.pitchData[k].radius = 0.03;
          if (
            this.pitchTypes[this.selectedPlayerData.pitchData[k].pitchName] ==
            null
          )
            this.pitchTypes[
              this.selectedPlayerData.pitchData[k].pitchName
            ] = {};

          this.pitchTypes[this.selectedPlayerData.pitchData[k].pitchName][
            this.selectedPlayerData.pitchData[k].pitchNum
          ] = this.selectedPlayerData.pitchData[k];
        }
        //console.log(this.pitchTypes);
        //console.log(this.selectedPlayerData.pitchData);
      },
    };
  },
  name: "App",
  components: {
    Player,
    Panel,
    PitchPlot,
  },
  methods: {
    getFilteredPitches(filterPitch) {
      if (this.selectedPlayerData.pitchData == null) return {};
      return this.selectedPlayerData.pitchData.filter(function (pitch) {
        return pitch.pitchType == filterPitch;
      });
    },
    sortPlayers(k) {
      this.playerRoster.sort(function (a, b) {
        if (a[k] < b[k]) return -1;
        else if (a[k] > b[k]) return 1;
        else return 1;
      });
    },
    getAllPlayers() {
      this.gotPlayerRoster = false;
      fetch("https://cle-fe-challenge-services.vercel.app/api/players", {
        method: "GET",
      })
        .then((response) => {
          if (response.ok) {
            return response.json();
          }
        })
        .then((response) => {
          this.gotPlayerRoster = true;
          this.playerRoster = response.players;
          this.sortPlayers(this.sortKey);
          //this.selectedPlayerData = this.playerRoster[0];

          return this.playerRoster;
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style lang="scss">
@import "./scss/_variables.scss";
/* background: red; */
#app {
  background-color: $pageBg;
  min-height: 100%;
}
</style>
