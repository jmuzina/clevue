<template>
  <table
    class="table table-striped table-bordered table-sm table-hover"
    v-on:mouseleave="stopHoverRow(hoveredPitch)"
  >
    <thead>
      <tr>
        <th
          v-on:click="sort('gameDate')"
          v-html="'Date ' + sortArrow('gameDate')"
        />
        <th
          v-on:click.prevent="sort('pitchNum')"
          v-html="'# ' + sortArrow('pitchNum')"
        />
        <th
          v-on:click.prevent="sort('pitchType')"
          v-html="'Pitch ' + sortArrow('pitchType')"
        />
        <th
          v-on:click.prevent="sort('balls')"
          v-html="'Balls ' + sortArrow('balls')"
        />
        <th
          v-on:click.prevent="sort('strikes')"
          v-html="'Strikes ' + sortArrow('strikes')"
        />
        <th
          v-on:click.prevent="sort('velo')"
          v-html="'MPH ' + sortArrow('velo')"
        />
        <th v-on:click.prevent="sort('x')" v-html="'X ' + sortArrow('x')" />
        <th v-on:click.prevent="sort('y')" v-html="'Y ' + sortArrow('y')" />
        <th
          v-on:click.prevent="sort('cut')"
          v-html="'Cut ' + sortArrow('cut')"
        />
        <th
          v-on:click.prevent="sort('rise')"
          v-html="'Rise ' + sortArrow('rise')"
        />
        <th
          v-on:click.prevent="sort('swing')"
          v-html="'Swung ' + sortArrow('swing')"
        />
        <th
          v-on:click.prevent="sort('miss')"
          v-html="'Missed ' + sortArrow('miss')"
        />
        <th
          v-on:click.prevent="sort('inStrikeZone')"
          v-html="'Strike ' + sortArrow('inStrikeZone')"
        />
        <th
          v-on:click.prevent="sort('batterShortName')"
          v-html="'Batter ' + sortArrow('batterShortName')"
        />
        <th
          v-on:click.prevent="sort('result')"
          v-html="'Result ' + sortArrow('result')"
        />
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="(pitch, index) in pitchesData"
        v-bind:item="pitch"
        v-bind:index="index"
        v-bind:key="pitch.pitchId"
        v-on:mouseover="hoverRow(pitch)"
        v-on:mouseleave="stopHoverRow(pitch)"
        :class="{ 'pitch-row-hover': pitch.pitchId === hoveredPitch }"
      >
        <td>{{ pitch.gameDate.toString().substring(0, 10) }}</td>
        <td>{{ pitch.pitchNum }}</td>
        <td>{{ pitch.pitchType }}</td>
        <td>{{ pitch.balls }}</td>
        <td>{{ pitch.strikes }}</td>
        <td>{{ pitch.velo.toFixed(1) }}</td>
        <td>{{ pitch.x.toFixed(1) }}</td>
        <td>{{ pitch.y.toFixed(1) }}</td>
        <td>{{ pitch.cut != null ? pitch.cut.toFixed(1) : "0" }}</td>
        <td>{{ pitch.rise != null ? pitch.rise.toFixed(1) : "0" }}</td>
        <td>{{ boolToStr(pitch.swing) }}</td>
        <td>{{ boolToStr(pitch.miss) }}</td>
        <td>{{ boolToStr(pitch.inStrikeZone) }}</td>
        <td>{{ pitch.batterShortName }}</td>
        <td>{{ pitch.result }}</td>
      </tr>
    </tbody>
  </table>
</template>
<script>
export default {
  props: {
    pitches: {
      type: Array,
      default: () => [],
    },
    selectedPitch: String(),
  },
  components: {},
  watch: {
    selectedPitch: function (newValue, oldValue) {
      this.hoveredPitch = newValue;
    },
  },
  data() {
    return {
      hoveredPitch: "",
      pitchesData: this.pitches,
      selectedSortingMetric: "gameDate",
      invertSort: false,
      sortArrow: function (metric) {
        if (metric === this.selectedSortingMetric) {
          return "&#" + (this.invertSort ? "8593" : "8595") + ";";
        } else return "";
      },
    };
  },
  computed: {
    hovered: function () {
      return this.hoveredPitch;
    },
  },
  methods: {
    hoverRow: function (pitch) {
      this.hoveredPitch = pitch.pitchId;
      pitch.isSelected = true;
      this.$emit("hovered-pitch", this.hoveredPitch);
    },
    stopHoverRow: function (pitch) {
      this.hoveredPitch = "";

      this.$emit("stop-hovered-pitch", pitch !== null ? pitch.pitchId : "");
    },
    coordinates: function (pitch) {
      return (
        pitch.x.toFixed(2).toString() + ", " + pitch.y.toFixed(2).toString()
      );
    },
    boolToStr: function (bool) {
      return bool ? "Y" : "N";
    },
    sort: function (metric) {
      this.invertSort =
        this.selectedSortingMetric === metric ? !this.invertSort : false;
      this.selectedSortingMetric = metric;
      const inverted = this.invertSort;

      this.pitchesData = this.pitchesData.sort(function (a, b) {
        if (!inverted) return a[metric] >= b[metric];
        else return a[metric] < b[metric];
      });
    },
  },
};
</script>
<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>