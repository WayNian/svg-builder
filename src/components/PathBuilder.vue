<template>
  <div>
    <svg
      :width="state.w"
      :height="state.h"
      @mousemove="handleMousemove"
      @click="handleClick"
      style="background-color: bisque; margin: 50px"
    >
      <path
        :d="generatePath()"
        stroke="black"
        stroke-width="5"
        fill="none"
      ></path>
      <g v-for="(item, index) in state.points" :key="index">
        <g v-if="item.q">
          <g>
            <line
              class="ad-Anchor-line"
              :x1="state.points[index - 1].x"
              :y1="state.points[index - 1].y"
              :x2="item.q.x"
              :y2="item.q.y"
            ></line>
            <line
              class="ad-Anchor-line"
              :x1="item.x"
              :y1="item.y"
              :x2="item.q.x"
              :y2="item.q.y"
            ></line>
            <circle
              class="ad-Anchor-point"
              :cx="item.q.x"
              :cy="item.q.y"
              r="8"
              fill="blue"
            ></circle>
          </g>
          <circle
            class="ad-Point"
            :cx="item.x"
            :cy="item.y"
            r="8"
            fill="blue"
          ></circle>
        </g>
        <g v-else-if="item.c">
          <g>
            <line
              class="ad-Anchor-line"
              :x1="state.points[index - 1].x"
              :y1="state.points[index - 1].y"
              :x2="item.c[0].x"
              :y2="item.c[0].y"
            ></line>
            <circle
              class="ad-Anchor-point"
              :cx="item.c[0].x"
              :cy="item.c[0].y"
              r="8"
              fill="blue"
            ></circle>
            <line
              class="ad-Anchor-line"
              :x1="item.x"
              :y1="item.y"
              :x2="item.c[1].x"
              :y2="item.c[1].y"
            ></line>
            <circle
              class="ad-Anchor-point"
              :cx="item.c[1].x"
              :cy="item.c[1].y"
              r="8"
              fill="blue"
            ></circle>
          </g>
          <circle
            class="ad-Point"
            :cx="item.x"
            :cy="item.y"
            r="8"
            fill="blue"
          ></circle>
        </g>
        <g v-else>
          <circle
            class="ad-Point"
            :cx="item.x"
            :cy="item.y"
            r="8"
            fill="blue"
          ></circle>
        </g>
      </g>
    </svg>
  </div>
</template>
<script setup>
import { reactive } from "vue";

const state = reactive({
  w: 800,
  h: 600,
  grid: {
    show: true,
    snap: true,
    size: 50,
  },
  ctrl: false,
  points: [
    { x: 100, y: 300 },
    { x: 200, y: 300, q: { x: 150, y: 50 } },
    { x: 300, y: 300, q: { x: 250, y: 550 } },
    { x: 400, y: 300, q: { x: 350, y: 50 } },
    {
      x: 500,
      y: 300,
      c: [
        { x: 450, y: 550 },
        { x: 450, y: 50 },
      ],
    },
    {
      x: 600,
      y: 300,
      c: [
        { x: 550, y: 50 },
        { x: 550, y: 550 },
      ],
    },
    { x: 700, y: 300, a: { rx: 50, ry: 50, rot: 0, laf: 1, sf: 1 } },
  ],
  activePoint: 2,
  draggedPoint: false,
  draggedQuadratic: false,
  draggedCubic: false,
  closePath: false,
});

const handleMousemove = (e) => {
  // console.log(e);
};
const handleClick = (e) => {
  console.log(e);
  state.points[0].x--;
  state.points[0].y--;
};

const generatePath = () => {
  let { points, closePath } = state;
  let d = "";

  points.forEach((p, i) => {
    if (i === 0) {
      // first point
      d += "M ";
    } else if (p.q) {
      // quadratic
      d += `Q ${p.q.x} ${p.q.y} `;
    } else if (p.c) {
      // cubic
      d += `C ${p.c[0].x} ${p.c[0].y} ${p.c[1].x} ${p.c[1].y} `;
    } else if (p.a) {
      // arc
      d += `A ${p.a.rx} ${p.a.ry} ${p.a.rot} ${p.a.laf} ${p.a.sf} `;
    } else {
      d += "L ";
    }

    d += `${p.x} ${p.y} `;
  });

  if (closePath) d += "Z";

  return d;
};
</script>
<style scoped>
.ad-Anchor-line {
  stroke: #888;
  stroke-width: 1px;
  stroke-dasharray: 5 5;
}
.ad-Anchor-point {
  cursor: pointer;
  fill: #fff;
  stroke: #888;
  stroke-width: 5px;
}
.ad-Point {
  cursor: pointer;
  fill: #fff;
  stroke: #555;
  stroke-width: 5px;
  -webkit-transition: fill 0.2s;
  -o-transition: fill 0.2s;
  transition: fill 0.2s;
}
</style>
