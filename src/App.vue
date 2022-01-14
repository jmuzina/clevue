<template>
  <div class="player-list" v-if="gotPlayerRoster == true" id="app">
    <!--
    <Player />
    -->
    <div
      class="player-container"
      v-for="(item, index) in playerRoster"
      v-bind:item="item"
      v-bind:index="index"
      v-bind:key="item.playerId"
    >
      <Player :playerId="item.playerId" />
    </div>
  </div>
</template>

<script>
import Player from "./components/Player";

export default {
  data() {
    return {
      playerRoster: this.getAllPlayers(),
      gotPlayerRoster: false,
    };
  },
  name: "App",
  components: {
    Player,
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
