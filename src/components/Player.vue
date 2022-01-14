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
  computed: {
    get_card_class_str: function () {
      let result = "player-card";
      if (this.$parent.selectedPlayerData.playerId === this.playerId)
        result += " selectedPlayer";

      return result;
    },
  },
  methods: {
    callAPI(callType) {
      this["got_" + callType] = false;
      fetch(
        "https://cle-fe-challenge-services.vercel.app/api/" +
          callType +
          "?playerId=" +
          this.playerId,
        {
          method: "GET",
        }
      )
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
      const obj = {
        playerId: this.playerId,
        pitchData: this.pitchesData,
      };
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