<template>
  <table
    class="table table-striped table-bordered table-sm table-hover"
    v-on:mouseleave="stopHoverRow(previewedPitch)"
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
    <tbody class="scrollablePitchDataTable">
      <tr
        v-for="(pitch, index) in pitchesData"
        v-bind:item="pitch"
        v-bind:index="index"
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
        <td>{{ pitch.velo.toFixed(1) }}</td>
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
  components: {},
  watch: {
    previewPitch: function (newValue, oldValue) {
      //console.log("tbl receive hover change from ", oldValue, "to", newValue);
      this.previewedPitch = newValue;
    },
    selectPitch: function (newValue, oldValue) {
      //console.log("tbl receive select change from ", oldValue, "to", newValue);
      this.selectedPitch = newValue;

      //console.log(
      //this.$el.querySelector("tbody").querySelector(".pitch-row-select")
      //);

      this.$nextTick(() => {
        var table = this.$el;
        var tableBody = table.querySelector("tbody");
        var selectedRow = tableBody.querySelector(".pitch-row-select");

        selectedRow.scrollIntoView({
          behavior: "smooth",
          block: "nearest",
          inline: "nearest",
        });
      });
    },
  },
  data() {
    return {
      previewedPitch: "",
      selectedPitch: "",
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
      return this.previewedPitch;
    },
  },
  methods: {
    clickRow: function (pitch) {
      //console.log("tbl select " + pitch.pitchId);
      this.selectedPitch = pitch.pitchId;
      //pitch.isSelected = true;
      this.$emit("clicked-pitch", this.selectedPitch);
    },
    hoverRow: function (pitch) {
      //console.log("tbl start hover " + pitch.pitchId);
      this.previewedPitch = pitch.pitchId;
      //pitch.isSelected = true;
      this.$emit("start-hovered-pitch", this.previewedPitch);
    },
    stopHoverRow: function (pitch) {
      //console.log("tbl stop hover " + pitch.pitchId);
      this.previewedPitch = "";

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