<template>
  <div class="container-fluid">
    <div class="player-primary row">
      <div class="player-photo-container col-4 col-lg-2">
        <img class="player-image" :src="imgUrl" alt="Player Image" />
      </div>
      <div class="player-details-container col-8 col-lg-10">
        <div class="player-name-container row">
          <span class="player-name">{{ $parent.playersData["fullName"] }}</span>
        </div>
        <div class="player-org-info-container row">
          <span class="player-contract-info">
            {{ $parent.playersData["orgAbbr"] }} ({{
              timeStr($parent.playersData["serviceTime"])
            }})
          </span>
        </div>
        <div class="player-bio-info-container row">
          <div class="player-bio-col col-4">
            <span>{{ $parent.playersData["position"] }}</span>
          </div>
          <div class="player-bio-col col-4">
            <span
              >Age:{{
                Math.floor(parseFloat($parent.playersData["ageDecimal"]))
              }}</span
            >
          </div>
          <div class="player-bio-col col-4">
            <span>Throws:{{ $parent.playersData["throws"] }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  computed: {
    imgUrl: function () {
      return (
        "https://img.mlbstatic.com/mlb-photos/image/upload/d_people:generic:headshot:67:current.png/w_426,q_auto:best/v1/people/" +
        this.$parent.playersData["imgId"] +
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