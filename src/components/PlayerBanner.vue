<template>
  <div class="player-banner">
    <!-- https://img.mlbstatic.com/mlb-photos/image/upload/d_people:generic:headshot:67:current.png/w_426,q_auto:best/v1/people/{imgid}/headshot/67/current -->
    <img src="" alt="Player Image" />
    <div class="player-info">
      <div class="player-name">Name</div>
      <div class="player-bio">age, pos, throws</div>
      <div class="player-contract-info">orgName | Svc</div>
    </div>
  </div>
</template>

<script>
export default {
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