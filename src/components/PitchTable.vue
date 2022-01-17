<template>
  <table class="table table-striped table-bordered table-sm table-hover">
    <thead>
      <tr>
        <th v-on:click="sort('gameDate')">Date</th>
        <th v-on:click.prevent="sort('pitchNum')">#</th>
        <th v-on:click.prevent="sort('pitchType')">Type</th>
        <th v-on:click.prevent="sort('balls')">Balls</th>
        <th v-on:click.prevent="sort('strikes')">Strikes</th>
        <th v-on:click.prevent="sort('velo')">MPH</th>
        <th v-on:click.prevent="sort('x')">X</th>
        <th v-on:click.prevent="sort('y')">Y</th>
        <th v-on:click.prevent="sort('cut')">Cut</th>
        <th v-on:click.prevent="sort('rise')">Rise</th>
        <th v-on:click.prevent="sort('swing')">Swing</th>
        <th v-on:click.prevent="sort('miss')">Miss</th>
        <th v-on:click.prevent="sort('inStrikeZone')">Strike</th>
        <th v-on:click.prevent="sort('batterShortName')">Batter</th>
        <th v-on:click.prevent="sort('result')">Result</th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="(pitch, index) in pitchesData"
        v-bind:item="pitch"
        v-bind:index="index"
        v-bind:key="pitchId(pitch)"
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
  },
  components: {},
  data() {
    return {
      pitchesData: this.pitches,
      selectedSortingMetric: "gameDate",
      invertSort: false,
    };
  },
  computed: {},
  methods: {
    pitchId: function (pitch) {
      return (
        pitch.gameId.toString() +
        "_" +
        pitch.pitcherId.toString() +
        "_" +
        pitch.pitchNum.toString()
      );
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