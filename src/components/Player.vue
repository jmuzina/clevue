<template>
  <div class="player-page">
    <PlayerBanner />
    <div>
      <Panel title="All Pitches">
        <PitchPlot />
      </Panel>
    </div>
  </div>
</template>
<script>
import PlayerBanner from "./PlayerBanner.vue";
import Panel from "./layout/Panel.vue";
import PitchPlot from "./plots/PitchPlot.vue";
export default {
  components: {
    PlayerBanner,
    Panel,
    PitchPlot,
  },
  data() {
    return {
      playerInfo: {},
    };
  },
};
</script>
<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>