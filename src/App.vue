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
            <PitchPlot />
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

      player_selected: function (selected) {
        this.selectedPlayerData = selected;
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
          response.players.sort(function (a, b) {
            const nameA = a.fullName.toUpperCase();
            const nameB = b.fullName.toUpperCase();
            if (nameA < nameB) return -1;
            else if (nameA > nameB) return 1;
            else return 0;
          });
          this.playerRoster = response.players;
          this.selectedPlayerData = response.players[0];
          return response.players;
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
