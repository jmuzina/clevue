<template>
  <div class="player-banner">
    <h2 class="player-name">{{ $parent.playerInfo["fullName"] }}</h2>
    <h4 class="player-contract-info">
      {{ $parent.playerInfo["orgAbbr"] }} ({{
        timeStr($parent.playerInfo["serviceTime"])
      }})
    </h4>
    <img :src="imgUrl" alt="Player Image" />
    <table class="player-info">
      <tr>
        <th>Age</th>
        <th>{{ Math.floor(parseFloat($parent.playerInfo["ageDecimal"])) }}</th>
      </tr>
      <tr>
        <th>Position</th>
        <th>{{ $parent.playerInfo["position"] }}</th>
      </tr>
      <tr>
        <th>Throws</th>
        <th>{{ $parent.playerInfo["throws"] }}</th>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  computed: {
    imgUrl: function () {
      return (
        "https://img.mlbstatic.com/mlb-photos/image/upload/d_people:generic:headshot:67:current.png/w_426,q_auto:best/v1/people/" +
        this.$parent.playerInfo["imgId"] +
        "/headshot/67/current"
      );
    },
  },
  props: {
    playerInfo: {
      type: Object,
      default: null,
    },
  },
  methods: {
    timeStr(decimalTime) {
      const numYears = Math.floor(decimalTime);
      const numMonths = Math.floor((decimalTime % 1) * 12);

      let yrsString = "";
      let monthsString = "";

      if (numYears !== 0) {
        yrsString = numYears.toString() + " year";
        if (numYears > 1) yrsString += "s";
      }

      if (numMonths !== 0) {
        monthsString = numMonths.toString() + " month";
        if (numMonths > 1) monthsString += "s";
      }

      if (numYears > 0 && numMonths > 0) yrsString += ",";
      else if (numYears === 0 && numMonths === 0) return "<1 month";

      let result = yrsString;
      if (result === "") result += monthsString;
      else if (monthsString !== "") result += " " + monthsString;

      return result;
    },
  },
};
</script>

<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>