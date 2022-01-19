<!-- 
  Pitch Table Component 
  Displays data for a given pitch type in tabular form.
    Table rows are interactive. 
      Hovering a row highlights it in the corresponding row plot while hovered.
      Clicking a row highlights it in the corresponding row plot until another pitch is selected.
      Hovers/clicks on the plot perform the same actions upon the table.
  J. Muzina
  01/18/2022
-->

<template>
  <table
    class="table table-striped table-bordered table-sm table-hover"
    v-on:mouseleave="stopHoverRow(previewedPitch)"
  >
    <thead>
      <tr>
        <!--Create interactive column headers for each pitch metric-->
        <th
          v-for="(displayName, metric) in metricNames"
          v-bind:key="metric"
          v-html="displayName + sortArrow(metric)"
          v-on:click="sort(metric)"
        ></th>
      </tr>
    </thead>
    <tbody class="scrollablePitchDataTable">
      <!--Add a table row for each pitch-->
      <tr
        v-for="pitch in pitchesData"
        v-bind:key="pitch.pitchId"
        v-on:mouseover="hoverRow(pitch)"
        v-on:mouseleave="stopHoverRow(pitch)"
        v-on:click.prevent="clickRow(pitch)"
        :class="{
          'pitch-row-select': pitch.pitchId === selectedPitch,
          'pitch-row-hover': pitch.pitchId === previewedPitch,
        }"
      >
        <td>{{ pitch.gameDate.toString().substring(0, 10) }}</td>
        <td>{{ pitch.pitchNum }}</td>
        <td>{{ pitch.pitchType }}</td>
        <td>{{ pitch.balls }}</td>
        <td>{{ pitch.strikes }}</td>
        <td>{{ (pitch.velo !== null ? pitch.velo : 0).toFixed(1) }}</td>
        <td>{{ pitch.x.toFixed(1) }}</td>
        <td>{{ pitch.y.toFixed(1) }}</td>
        <td>{{ pitch.cut != null ? pitch.cut.toFixed(1) : "0" }}</td>
        <td>{{ pitch.rise != null ? pitch.rise.toFixed(1) : "0" }}</td>
        <td>{{ boolToStr(pitch.swing) }}</td>
        <td>{{ boolToStr(pitch.miss) }}</td>
        <td>{{ boolToStr(pitch.inStrikeZone) }}</td>
        <td>
          {{
            pitch.batterShortName.substring(0, pitch.batterShortName.length - 1)
          }}
        </td>
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
    previewPitch: String(),
    selectPitch: String(),
  },

  watch: {
    pitches: function (newValue, oldValue) {
      this.pitchesData = newValue;
    },
    previewPitch: function (newValue, oldValue) {
      this.previewedPitch = newValue;
    },
    selectPitch: function (newValue, oldValue) {
      this.selectedPitch = newValue;

      // Find the selected table row and scroll it into view.
      // This must be done on the next tick, after the selected class has been applied.
      this.$nextTick(() => {
        var table = this.$el;
        var tableBody = table.querySelector("tbody");
        var selectedRow = tableBody.querySelector(".pitch-row-select");

        selectedRow.scrollIntoView({
          behavior: "smooth",
          block: "center",
          inline: "nearest",
        });
      });
    },
  },

  computed: {
    hovered: function () {
      return this.previewedPitch;
    },
  },

  data() {
    return {
      previewedPitch: "",
      selectedPitch: "",
      pitchesData: this.pitches,
      invertSort: false,

      // The current metric used for sorting, defaults to game date
      selectedSortingMetric: "gameDate",

      // Pitch attributes to display in the table in form key: displayName
      metricNames: {
        gameDate: "Date",
        pitchNum: "#",
        pitchType: "Pitch",
        balls: "Balls",
        strikes: "Strikes",
        velo: "MPH",
        x: "X",
        y: "Y",
        cut: "Cut",
        rise: "Rise",
        swing: "Swung",
        miss: "Missed",
        inStrikeZone: "Strike",
        batterShortName: "Batter",
        result: "Result",
      },
    };
  },

  methods: {
    clickRow: function (pitch) {
      this.selectedPitch = pitch.pitchId;
      this.$emit("clicked-pitch", this.selectedPitch);
    },
    hoverRow: function (pitch) {
      this.previewedPitch = pitch.pitchId;
      this.$emit("start-hovered-pitch", this.previewedPitch);
    },
    stopHoverRow: function (pitch) {
      this.previewedPitch = "";
      this.$emit("stop-hovered-pitch", pitch !== null ? pitch.pitchId : "");
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
    // Get a unicode arrow to indicate sorting direction
    sortArrow: function (metric) {
      if (metric === this.selectedSortingMetric) {
        return "&#" + (this.invertSort ? "8593" : "8595") + ";";
      } else return "";
    },
  },
};
</script>
<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>