<!-- 
  Pitch Plot Component 
  Displays pitches of a given type graphically, on top of a strike zone.
    Plotted pitches are interactive. 
      Hovering a pitch highlights it in the corresponding table row while hovered.
      Clicking a pitch highlights it in the corresponding table until another pitch is selected.
      Hovers/clicks on table rows perform the same actions upon the plot.
  Cleveland Guardians, modified by J. Muzina
  01/18/2022
-->

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
        <!--Plot all pitches. Hovered and selected pitches are made larger for emphasis.-->
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
              (p.pitchId === previewedPitch ? 1.8 : 1) *
              (p.pitchId === selectedPitch ? 2.6 : 1) *
              (p.inStrikeZone ? 1 : 0.8)
            "
            :fill="p.fill"
            :fill-opacity="p.isSelected ? 1 : p.fillOpacity"
            :stroke="p.isSelected ? p.selectedStroke : p.stroke"
            :stroke-opacity="
              p.isSelected ? p.selectedStrokeOpacity : p.strokeOpacity
            "
            :stroke-width="p.isSelected ? p.selectedStrokeWidth : p.strokeWidth"
            v-on:mouseover="startHoverPitch(p)"
            v-on:mouseleave="stopHoverPitch(p)"
            v-on:click.prevent="clickPitch(p)"
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
    selectPitch: String(),
    previewPitch: String(),
  },

  watch: {
    previewPitch: function (newValue, oldValue) {
      this.previewedPitch = newValue;
    },
    selectPitch: function (newValue, oldValue) {
      this.selectedPitch = newValue;
    },
  },

  computed: {
    selectableItems() {
      return Object.entries(this.pitches).filter((p) => p.isSelectable);
    },
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
      previewedPitch: "",
      selectedPitch: "",

      coordSystem,
      strikezoneCoords: {
        x: -8.5 / 12,
        y: 40 / 12,
        width: 17 / 12,
        height: 20 / 12,
      },
    };
  },
  methods: {
    clickPitch: function (pitch) {
      this.selectedPitch = pitch.pitchId;
      pitch.isSelected = true;
      this.$emit("clicked-pitch", this.selectedPitch);
    },
    startHoverPitch: function (pitch) {
      this.previewedPitch = pitch.pitchId;
      pitch.isSelected = true;
      this.$emit("start-hovered-pitch", this.previewedPitch);
    },
    stopHoverPitch: function (pitch) {
      this.previewedPitch = "";
      pitch.isSelected = false;
      this.$emit("stop-hovered-pitch", pitch.pitchId);
    },
    scaleY(v) {
      return this.coordSystem.maxY - v + this.coordSystem.minY;
    },
  },
};
</script>

<style scoped lang="scss">
</style>
