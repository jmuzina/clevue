<template>
  <div class="row">
    <div class="pitchPlotContainer col-lg-4">
      <Panel :title="title">
        <PitchPlot
          v-on:hovered-pitch="hoveredPitch($event)"
          v-on:stop-hovered-pitch="stopHoveredPitch($event)"
          :pitches="pitchData"
          :selectedPitch="currentSelectedPitch"
        />
      </Panel>
    </div>
    <div class="pitchTableContainer col-lg-8">
      <PitchTable
        v-on:hovered-pitch="hoveredPitch($event)"
        v-on:stop-hovered-pitch="stopHoveredPitch($event)"
        :pitches="pitchData"
        :selectedPitch="currentSelectedPitch"
      />
    </div>
  </div>
</template>
<script>
import Panel from "./layout/Panel.vue";
import PitchPlot from "./plots/PitchPlot.vue";
import PitchTable from "./PitchTable.vue";

export default {
  props: {
    pitches: {
      type: Array,
      default: () => [],
    },
    title: {
      type: String,
      default: () => "All Pitches",
    },
  },
  components: {
    Panel,
    PitchPlot,
    PitchTable,
  },
  data() {
    return {
      pitchData: this.pitches,
      selectedPitch: "",
    };
  },
  computed: {
    currentSelectedPitch: function () {
      return this.selectedPitch;
    },
  },
  methods: {
    hoveredPitch: function (pitchId) {
      this.selectedPitch = pitchId;
    },
    stopHoveredPitch: function (pitchId) {
      this.selectedPitch = "";
    },
  },
};
</script>
<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>