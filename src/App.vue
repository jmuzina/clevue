<template>
  <div class="container-fluid" v-if="gotPlayerRoster == true" id="app">
    <div class="main-row row">
      <div class="player-list col-lg-3" v-if="gotPlayerRoster == true">
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
          />
        </div>
      </div>

      <div v-if="windowWidth > 992" class="pitchData col-lg-9">
        <p v-if="pitchTypes.length == 0" class="select-player-hint">
          Please select a player!
        </p>
        <PitchRow
          v-for="(pitches, pitchType) in playerPitchData[
            selectedPlayerData.playerId
          ]"
          v-bind:key="pitchType"
          :title="getPitchTitle(pitchType)"
          :pitches="pitches"
        />
      </div>
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
                <h5 class="modal-title" id="exampleModalLongTitle">
                  Pitch Statistics
                </h5>
                <button
                  type="button"
                  class="close"
                  data-dismiss="modal"
                  aria-label="Close"
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
                <button
                  type="button"
                  class="btn btn-secondary"
                  data-dismiss="modal"
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
      sortAscending: true,
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

      getPitchTitle: function (pitchType) {
        let numPitches = objLength(
          this.playerPitchData[this.selectedPlayerData.playerId][pitchType]
        );
        let numTotalPitches = objLength(
          this.playerPitchData[this.selectedPlayerData.playerId]["All Pitches"]
        );
        let result = pitchType + " (" + numPitches + ")";

        if (numPitches !== numTotalPitches) {
          const percentage = ((numPitches / numTotalPitches) * 100).toFixed(1);
          result += " (" + percentage + "%)";
        }
        return result;
      },

      player_selected: function (selected) {
        this.selectedPlayerData = selected;
        const pid = this.selectedPlayerData.playerId;
        if (this.playerPitchData[pid] == null) {
          this.pitchTypes = [];
          this.playerPitchData[pid] = {
            //"All Pitches": {},
            "All Pitches": [],
          };
          let pitchTypesList = {};
          for (const k in this.selectedPlayerData.pitchData) {
            let pitch = this.selectedPlayerData.pitchData[k];
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

          let pitchKeys = Object.entries(pitchTypesList);
          pitchKeys.sort(function (a, b) {
            if (a[0] < b[0]) return -1;
            else if (a[0] > b[0]) return 1;
            else return 0;
          });

          for (const i in pitchKeys) {
            const pitchType = pitchKeys[i][0];
            const pitchList = pitchKeys[i][1];

            if (this.pitchTypes.indexOf(pitchType) === -1)
              this.pitchTypes.push(pitchKeys[i]);
            if (this.playerPitchData[pid][pitchType] == null)
              this.playerPitchData[pid][pitchType] = pitchList;
          }
        }
      },
    };
  },
  name: "App",
  components: {
    Player,
    PitchRow,
  },
  methods: {
    getFilteredPitches(filterPitch) {
      if (this.selectedPlayerData.pitchData == null) return {};
      return this.selectedPlayerData.pitchData.filter(function (pitch) {
        return pitch.pitchType === filterPitch;
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
