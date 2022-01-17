<template>
  <div class="row">
    <div class="pitchPlotContainer col-lg-4">
      <Panel :title="title">
        <PitchPlot :pitches="pitches" />
      </Panel>
    </div>
    <div class="pitchTableContainer col-lg-8">
      <PitchTable :pitches="pitches" />
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
    return {};
  },
  computed: {},
  methods: {},
};
</script>
<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>