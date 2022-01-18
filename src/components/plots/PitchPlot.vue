<template>
  <div ref="container" class="pitch-plot-container">
    <div class="pitch-plot">
      <svg
        style="display: block"
        :viewBox="
          coordSystem.minX +
          ' ' +
          coordSystem.minY +
          ' ' +
          coordSystem.width +
          ' ' +
          coordSystem.height
        "
        preserveAspectRatio="xMidYMid meet"
        :style="{ 'background-color': backgroundColor }"
        ref="svg"
        @mouseup="mouseupFunc($event)"
        @mousedown="mousedownFunc($event)"
        @mousemove="mousemoveFunc($event)"
      >
        <template v-for="(p, i) in pitches">
          <circle
            :key="'pitch-' + i"
            v-show="p.isVisible && !p.hidden"
            :class="[
              p.classList,
              { visible: p.isVisible },
              { selected: p.isSelected },
            ]"
            :cx="p.x"
            :cy="scaleY(p.y)"
            :r="
              p.radius *
              (p.pitchId === hoveredPitch ? 2 : 1) *
              (p.inStrikeZone ? 1 : 0.8)
            "
            :fill="p.fill"
            :fill-opacity="p.isSelected ? 1 : p.fillOpacity"
            :stroke="p.isSelected ? p.selectedStroke : p.stroke"
            :stroke-opacity="
              p.isSelected ? p.selectedStrokeOpacity : p.strokeOpacity
            "
            :stroke-width="p.isSelected ? p.selectedStrokeWidth : p.strokeWidth"
            v-on:mouseover="hoverPitch(p)"
            v-on:mouseleave="stopHoverPitch(p)"
          />
        </template>
        <rect
          :x="strikezoneCoords.x"
          :y="scaleY(strikezoneCoords.y)"
          :width="strikezoneCoords.width"
          :height="strikezoneCoords.height"
          stroke="#000000"
          :stroke-width="0.02"
          fill-opacity="0"
          style="pointer-events: none"
        ></rect>

        <rect
          :x="lassoCoords.x"
          :y="lassoCoords.y"
          :width="lassoCoords.width"
          :height="lassoCoords.height"
          stroke="#000000"
          :stroke-width="0.04"
          fill-opacity="0"
          style="pointer-events: none"
        ></rect>
      </svg>
    </div>
  </div>
</template>

<script>
/*

Example pitches 
// [
// 		{
//			x: 0, -- REQUIRED - center of ball - in feet
//			y: 2.17, -- REQUIRED -center of ball - in feet
// 			stroke: 'black',-- REQUIRED - outline color
// 			strokeWidth: .01, -- REQUIRED - outline width - in feet
// 			strokeOpacity: 1, -- REQUIRED - outline opacity
// 			selectedStroke: 'gold', -- OPTIONAL - selected outline color
// 			selectedStrokeWidth: 2,	-- OPTIONAL - selected stroke width - in feet
// 			fillOpacity: 1,	-- REQUIRED - fill opacity
// 			fill: 'blue', -- REQUIRED - fill color
// 			radius: 1.5/12, -- REQUIRED - in feet
// 			isSelected: false, -- OPTIONAL
//			isSelectable: true, -- OPTIONAL (if falsy, balls cannot be selected)
// 			isVisible: true, -- REQUIRED
// 		},
// ]

 */
import { selectableMixin } from "../../utility/selectable";

export default {
  mixins: [selectableMixin],
  props: {
    backgroundColor: {
      default: "#DFDFDF",
      type: String,
    },
    pitches: {
      type: Array,
      default: () => [],
    },
    selectedPitch: String(),
  },

  data() {
    const min = 0;
    const max = 5;
    const coordSystem = {
      minX: 0 - (max - min) / 2,
      maxX: 0 + (max - min) / 2,
      minY: 0,
      maxY: 4.3,
      width: (max - min) * 1,
      height: (max - min) * 0.77,
    };

    return {
      height: null,
      svg: null,
      hoveredPitch: "",

      coordSystem,
      strikezoneCoords: {
        x: -8.5 / 12,
        y: 40 / 12,
        width: 17 / 12,
        height: 20 / 12,
      },
    };
  },
  computed: {
    selectableItems() {
      return Object.entries(this.pitches).filter((p) => p.isSelectable);
    },
  },
  watch: {
    selectedPitch: function (newValue, oldValue) {
      this.hoveredPitch = newValue;
    },
  },
  methods: {
    hoverPitch: function (pitch) {
      this.hoveredPitch = pitch.pitchId;
      pitch.isSelected = true;
      this.$emit("hovered-pitch", this.hoveredPitch);
    },
    stopHoverPitch: function (pitch) {
      this.hoveredPitch = "";
      pitch.isSelected = false;
      this.$emit("stop-hovered-pitch", pitch.pitchId);
    },
    scaleY(v) {
      return this.coordSystem.maxY - v + this.coordSystem.minY;
    },
    /*
    isStrike(pitch) {
      const lowerBoundX = this.strikezoneCoords.x;
      const upperBoundX = this.strikezoneCoords.x + this.strikezoneCoords.width;
      const lowerBoundY = this.strikezoneCoords.y * 0.5;
      const upperBoundY =
        (this.strikezoneCoords.y + this.strikezoneCoords.height) * 0.67;

      if (pitch.x >= upperBoundX) return false;
      if (pitch.x <= lowerBoundX) return false;
      if (pitch.y >= upperBoundY) return false;
      if (pitch.y <= lowerBoundY) return false;

      return true;
    },
    */
  },
};
</script>

<style scoped lang="scss">
</style>
