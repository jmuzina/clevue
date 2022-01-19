<!-- 
  Cleveland Guardians Coding Challenge
  Gets and displays information about pitchers, including biographical data and pitching metrics.
  J. Muzina
  01/18/2022
-->

<template>
  <div class="container-fluid" v-if="gotPlayerRoster == true" id="app">
    <div class="main-row row">
      <div class="player-list col-lg-3" v-if="gotPlayerRoster == true">
        <!--Create a Player card for each player found-->
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
            data-toggle="modal"
            data-target="#pitchDataModal"
            data-backdrop="static"
            data-keyboard="false"
          />
        </div>
      </div>

      <!--Display pitch information in a second column on large displays-->
      <div v-if="windowWidth > 992" class="pitchData col-lg-9">
        <p v-if="pitchTypes.length == 0" class="select-player-hint">
          Please select a player!
        </p>
        <!--Create a pitch row for each pitch type found in player's pitch data cache-->
        <PitchRow
          v-for="(pitches, pitchType) in playerPitchData[
            selectedPlayerData.playerId
          ]"
          v-bind:key="pitchType"
          :title="getPitchTitle(pitchType)"
          :pitches="pitches"
        />
      </div>
      <!--Display pitch information in a pop-up modal on smaller displays-->
      <div v-else class="pitchModal">
        <div
          class="modal fade"
          id="pitchDataModal"
          tabindex="-1"
          role="dialog"
          aria-labelledby="pitchDataModalTitle"
          aria-hidden="true"
        >
          <div class="modal-dialog modal-dialog-centered" role="document">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title" id="pitchDataModalTitle">
                  Pitch Statistics
                </h5>
                <!--Deselect player on modal close to allow re-selection of the same player-->
                <button
                  type="button"
                  class="close"
                  data-dismiss="modal"
                  aria-label="Close"
                  v-on:click="player_deselected"
                >
                  <span aria-hidden="true">&times;</span>
                </button>
              </div>
              <div class="modal-body">
                <PitchRow
                  v-for="(pitches, pitchType) in playerPitchData[
                    selectedPlayerData.playerId
                  ]"
                  v-bind:key="pitchType"
                  :title="getPitchTitle(pitchType)"
                  :pitches="pitches"
                />
              </div>
              <div class="modal-footer">
                <!--Deselect player on modal close to allow re-selection of the same player-->
                <button
                  type="button"
                  class="btn btn-secondary"
                  data-dismiss="modal"
                  v-on:click="player_deselected"
                >
                  Close
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Player from "./components/Player";
import PitchRow from "./components/PitchRow.vue";

function objLength(obj) {
  let result = 0;
  for (const k in obj) {
    ++result;
  }
  return result;
}

export default {
  name: "App",
  components: {
    Player,
    PitchRow,
  },
  mounted() {
    window.onresize = () => {
      this.windowWidth = window.innerWidth;
    };
  },
  data() {
    return {
      playerRoster: this.getAllPlayers(),
      gotPlayerRoster: false,
      playerPitchData: {},
      selectedPlayerData: {},
      sortKey: "fullName",
      windowWidth: window.innerWidth,

      pitchTypes: [],

      pitchTypeColors: {
        Fastball: "blue",
        Curveball: "red",
        Slider: "green",
        Changeup: "purple",
        Sinker: "teal",
        Cutter: "orange",
      },
    };
  },
  methods: {
    // Given a pitch type, return a string including the pitch name, the number of pitches thrown, and that pitch's use frequency
    getPitchTitle: function (pitchType) {
      let numPitches = objLength(
        this.playerPitchData[this.selectedPlayerData.playerId][pitchType]
      );
      let numTotalPitches = objLength(
        this.playerPitchData[this.selectedPlayerData.playerId]["All Pitches"]
      );
      let result = pitchType + " (" + numPitches + ")";

      // Display pitch frequency percentage unless pitchType === "All Pitches"
      if (numPitches !== numTotalPitches) {
        const percentage = ((numPitches / numTotalPitches) * 100).toFixed(1);
        result += " (" + percentage + "%)";
      }
      return result;
    },
    // Get all pitches of a specific pitch type
    getFilteredPitches(filterPitch) {
      if (this.selectedPlayerData.pitchData == null) return {};
      return this.selectedPlayerData.pitchData.filter(function (pitch) {
        return pitch.pitchType === filterPitch;
      });
    },
    // Sort all players by a given metric
    sortPlayers(k) {
      this.playerRoster.sort(function (a, b) {
        if (a[k] < b[k]) return -1;
        else if (a[k] > b[k]) return 1;
        else return 0;
      });
    },
    // Query API for list of all players
    getAllPlayers() {
      this.gotPlayerRoster = false;
      let url = "https://cle-be-challenge-1.vercel.app/api?calltype=players"; // custom endpoint
      // let url = "https://cle-fe-challenge-services.vercel.app/api/players;" // provided endpoint
      fetch(url, {
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
          return this.playerRoster;
        })
        .catch((err) => {
          console.log("fetch err", err);
        });
    },
    pitchColor(pitch) {
      if (pitch.pitchName in this.pitchTypeColors)
        return this.pitchTypeColors[pitch.pitchName];

      return "black";
    },
    // Cache the selected player's pitch data in a list of the form
    // pitchData[pid] {
    //  "All Pitches":  [pitch1, pitch2, ..., pitchN]
    //  pitchName1:     [pitch1, pitch2, ..., pitchN]
    //  pitchName2:     [pitch1, pitch2, ..., pitchN] ...
    //  pitchNameN:     [pitch1, pitch2, ..., pitchN]
    // }
    cachePitchData: function (pid) {
      this.pitchTypes = [];
      this.playerPitchData[pid] = {
        "All Pitches": [],
      };
      let pitchTypesList = {};

      for (const k in this.selectedPlayerData.pitchData) {
        let pitch = this.selectedPlayerData.pitchData[k];
        // Set pitch visibility and add a unique identifier to each pitch
        pitch.isVisible = true;
        pitch.radius = 0.04;
        pitch.fill = this.pitchColor(pitch);
        pitch.pitchId =
          pitch.gameId.toString() +
          "_" +
          pitch.pitcherId.toString() +
          "_" +
          pitch.pitchNum.toString();

        if (pitchTypesList[pitch.pitchName] == null)
          pitchTypesList[pitch.pitchName] = [];

        pitchTypesList[pitch.pitchName].push(pitch);
        this.playerPitchData[pid]["All Pitches"].push(pitch);
        this.selectedPlayerData.pitchData[k] = pitch;
      }

      // Sort pitch types alphabetically
      let pitchKeys = Object.entries(pitchTypesList);
      pitchKeys.sort(function (a, b) {
        if (a[0] < b[0]) return -1;
        else if (a[0] > b[0]) return 1;
        else return 0;
      });

      // Push all pitch arrays into playerPitchData alphabetically
      for (const i in pitchKeys) {
        const pitchType = pitchKeys[i][0];
        const pitchList = pitchKeys[i][1];

        if (this.pitchTypes.indexOf(pitchType) === -1)
          this.pitchTypes.push(pitchKeys[i]);
        if (this.playerPitchData[pid][pitchType] == null)
          this.playerPitchData[pid][pitchType] = pitchList;
      }
    },
    player_selected: function (selected) {
      this.selectedPlayerData = selected;

      // Generate and cache player's pitch data if it has not been cached yet
      if (this.playerPitchData[this.selectedPlayerData.playerId] == null)
        this.cachePitchData(this.selectedPlayerData.playerId);
    },
    player_deselected: function () {
      this.selectedPlayerData = {};
    },
  },
};
</script>

<style lang="scss">
@import "./scss/_variables.scss";
#app {
  background-color: $pageBg;
  min-height: 100%;
}
</style>
