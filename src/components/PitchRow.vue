<!-- 
  Pitch Row Component 
  Contains a pitch plot and data table for a given pitch type, arranged in a row.
    Receives pitch hovers and selections from the pitch plot and data table, and forwards them to the other component.
  J. Muzina
  01/18/2022
-->

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
        :selectedPitch="selectedPitch"
        :displayWidth="displayWidth"
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
    selectedPitch: String(),
    displayWidth: Number(),
  },

  components: {
    Panel,
    PitchPlot,
    PitchTable,
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

  data() {
    return {
      pitchData: this.pitches,
      hoveredPitch: "",
    };
  },

  // Forward pitch hovers/clicks from the interacted component to the opposite component.
  methods: {
    startHoveredPitch: function (pitchId) {
      this.hoveredPitch = pitchId;
    },
    stopHoveredPitch: function (pitchId) {
      this.hoveredPitch = "";
    },
    selectPitch: function (pitchId) {
      this.$emit("clicked-pitch", pitchId);
    },
  },
};
</script>
<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>