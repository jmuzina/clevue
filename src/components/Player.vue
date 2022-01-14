<template>
  <div v-if="gotPlayerData == true" class="player-card">
    <div class="player-details">
      <PlayerBanner />
      <!--
      <div class="pitchPlot">
        <Panel title="All Pitches">
          <PitchPlot />
        </Panel>
      </div>
      -->
    </div>
  </div>
</template>
<script>
import PlayerBanner from "./PlayerBanner.vue";
import Panel from "./layout/Panel.vue";
import PitchPlot from "./plots/PitchPlot.vue";
export default {
  props: ["playerId"],
  components: {
    PlayerBanner,
    Panel,
    PitchPlot,
  },
  data() {
    return {
      playerInfo: this.getPlayerData(),
      gotPlayerData: false,
    };
  },
  methods: {
    getPlayerData() {
      this.gotPlayerData = false;
      fetch(
        "https://cle-fe-challenge-services.vercel.app/api/players?playerId=" +
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
          this.gotPlayerData = true;
          this.playerInfo = response.playerDetail;
          return response.playerDetail;
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>
<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>