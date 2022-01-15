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
          <Panel
            v-for="(pitches, pitchType) in playerPitchData[
              selectedPlayerData.playerId
            ]"
            v-bind:item="pitches"
            v-bind:index="pitchType"
            v-bind:key="pitchType"
            :title="pitchType"
          >
            <PitchPlot :pitches="Object.values(pitches)" />
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
      playerPitchData: {},
      selectedPlayerData: {},
      sortAscending: true,
      sortKey: "fullName",

      pitchTypes: [],

      pitchTypeColors: {
        Fastball: "blue",
        Curveball: "red",
        Slider: "green",
        Changeup: "purple",
        Sinker: "teal",
        Cutter: "orange",
      },

      player_selected: function (selected) {
        this.selectedPlayerData = selected;
        const pid = this.selectedPlayerData.playerId;
        if (this.playerPitchData[pid] == null) {
          this.pitchTypes = [];
          this.playerPitchData[pid] = {
            "All Pitches": [],
          };
          let pitchTypesList = {};
          for (const k in this.selectedPlayerData.pitchData) {
            this.selectedPlayerData.pitchData[k].isVisible = true;
            this.selectedPlayerData.pitchData[k].radius = 0.04;
            this.selectedPlayerData.pitchData[k].fill = this.pitchColor(
              this.selectedPlayerData.pitchData[k]
            );

            if (
              pitchTypesList[this.selectedPlayerData.pitchData[k].pitchName] ==
              null
            )
              pitchTypesList[
                this.selectedPlayerData.pitchData[k].pitchName
              ] = [];

            pitchTypesList[this.selectedPlayerData.pitchData[k].pitchName].push(
              this.selectedPlayerData.pitchData[k]
            );
            this.playerPitchData[pid]["All Pitches"].push(
              this.selectedPlayerData.pitchData[k]
            );
          }

          let pitchKeys = Object.entries(pitchTypesList);
          pitchKeys.sort(function (a, b) {
            if (a[0] < b[0]) return -1;
            else if (a[0] > b[0]) return 1;
            else return 0;
          });

          for (const i in pitchKeys) {
            //console.log(pitchKeys);
            const pitchType = pitchKeys[i][0];
            const pitchList = pitchKeys[i][1];

            if (this.pitchTypes.indexOf(pitchType) === -1)
              this.pitchTypes.push(pitchKeys[i]);
            if (this.playerPitchData[pid][pitchType] == null)
              this.playerPitchData[pid][pitchType] = Object.values(pitchList);

            //console.log("pitch list for this player");
            //console.log(pitchList);
          }
          //console.log("full data");
          //console.log(this.playerPitchData);
          console.log("generated data for ", pid, this.playerPitchData[pid]);
        } else {
          console.log("already have data for ", pid, this.playerPitchData[pid]);
        }
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
        else return 0;
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
    pitchColor(pitch) {
      if (pitch.pitchName in this.pitchTypeColors)
        return this.pitchTypeColors[pitch.pitchName];

      return "black";
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
