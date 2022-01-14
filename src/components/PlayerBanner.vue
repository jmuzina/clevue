<template>
  <div class="player-banner">
    <h2 class="player-name">{{ $parent.playerInfo["fullName"] }}</h2>
    <h4 class="player-contract-info">
      {{ $parent.playerInfo["orgAbbr"] }} ({{
        $parent.playerInfo["serviceTime"]
      }}
      yrs.)
    </h4>
    <img :src="imgUrl" alt="Player Image" />
    <table class="player-info">
      <tr>
        <th>Age</th>
        <th>{{ $parent.playerInfo["ageDecimal"] }}</th>
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
};
</script>

<style lang="scss" scoped>
@import "../scss/_variables.scss";
</style>