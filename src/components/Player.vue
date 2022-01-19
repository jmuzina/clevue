<!-- 
  Player card component
  Container for basic information about a player, allows the player to be selected
  Cleveland Guardians, modified by J. Muzina
  01/18/2022
-->

<template>
  <div
    v-if="got_players == true"
    :class="get_card_class_str"
    v-on:click="select_player"
  >
    <div class="player-details">
      <PlayerBanner />
    </div>
  </div>
</template>
<script>
import PlayerBanner from "./PlayerBanner.vue";
export default {
  props: ["playerId"],

  components: {
    PlayerBanner,
  },

  computed: {
    // Add a highlight class to a player card element if it is selected
    get_card_class_str: function () {
      let result = "player-card";
      if (this.$parent.selectedPlayerData.playerId === this.playerId)
        result += " selectedPlayer";

      return result;
    },
  },

  data() {
    return {
      playersData: this.callAPI("players"),
      pitchesData: this.callAPI("pitches"),
      got_players: false,
      got_pitches: false,
      apiTypes: {
        players: "playerDetail",
        pitches: "pitches",
      },
    };
  },

  methods: {
    // Query a specified API endpoint for a player's data
    callAPI(callType) {
      this["got_" + callType] = false;

      let url = "https://cle-be-challenge-1.vercel.app/api"; // custom endpoint
      // let url = "https://cle-fe-challenge-services.vercel.app/api" // provided endpoint
      fetch(url + "?calltype=" + callType + "&pid=" + this.playerId, {
        method: "GET",
      })
        .then((response) => {
          if (response.ok) {
            return response.json();
          }
        })
        .then((response) => {
          this["got_" + callType] = true;
          this[callType + "Data"] = response[this.apiTypes[callType]];
          return this[callType + "Data"];
        })
        .catch((err) => {
          console.log(err);
        });
    },
    select_player(event) {
      this.$emit("player-selected", {
        playerId: this.playerId,
        pitchData: this.pitchesData,
      });
    },
  },
};
</script>
<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>