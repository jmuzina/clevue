<template>
  <div class="row">
    <div class="pitchPlotContainer col-lg-4">
      <Panel :title="title">
        <PitchPlot
          v-on:start-hovered-pitch="startHoveredPitch($event)"
          v-on:stop-hovered-pitch="stopHoveredPitch($event)"
          v-on:clicked-pitch="selectPitch($event)"
          :pitches="pitchData"
          :previewPitch="currentHoveredPitch"
          :selectPitch="currentSelectedPitch"
        />
      </Panel>
    </div>
    <div class="pitchTableContainer col-lg-8">
      <PitchTable
        v-on:start-hovered-pitch="startHoveredPitch($event)"
        v-on:stop-hovered-pitch="stopHoveredPitch($event)"
        v-on:clicked-pitch="selectPitch($event)"
        :pitches="pitchData"
        :previewPitch="currentHoveredPitch"
        :selectPitch="currentSelectedPitch"
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
      hoveredPitch: "",
      selectedPitch: "",
    };
  },
  watch: {
    pitches: function (newV, oldV) {
      this.pitchData = newV;
    },
  },
  computed: {
    currentHoveredPitch: function () {
      return this.hoveredPitch;
    },
    currentSelectedPitch: function () {
      return this.selectedPitch;
    },
  },
  methods: {
    startHoveredPitch: function (pitchId) {
      //console.log("row start hover " + pitchId);
      this.hoveredPitch = pitchId;
    },
    stopHoveredPitch: function (pitchId) {
      //console.log("row stop hover " + pitchId);
      this.hoveredPitch = "";
    },
    selectPitch: function (pitchId) {
      //console.log("row select " + pitchId);
      this.selectedPitch = pitchId;
    },
  },
};
</script>
<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>