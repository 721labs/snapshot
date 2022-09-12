<script setup lang="ts">
import { ref, watch } from 'vue';
import { percentageOfTotal } from '@snapshot-labs/snapshot.js/src/voting/quadratic';
import { useMediaQuery } from '@vueuse/core';
import { computed } from 'vue';

// export default {
//   props: ['vote']
// };

const props = defineProps<{
  vote: number;
  credits: number;
}>();

console.log('credits', props.credits);

let row = 1;
let cyIncrement = 1;
let cxMap = [7, 21, 35, 49, 63];
let index = 0;
const circles = [...Array(100)].map((x, item) => {
  if (item > 0) {
    index++;
  }

  if (item > 0 && item % 5 === 0) {
    row = row + 1;
    cyIncrement = cyIncrement + 2;
    index = 0;
  }

  //   console.log(
  //     `row: ${row} item: ${item} cx: ${cxIncrement * 7} cy: ${cyMap[index]}`
  //   );

  return {
    id: `pool-${item}`,
    link: `#pool-${item}`,
    cy: cyIncrement * 7,
    cx: cxMap[index]
  };
});
</script>

<template>
  <div class="sticky top-0 flex justify-center">
    <svg
      width="100"
      height="300"
      viewBox="0 0 100 300"
      xmlns="http://www.w3.org/2000/svg"
      overflow="inherit"
    >
      <g fill="none" fillRule="evenodd">
        <circle
          v-for="bgCircle in circles"
          :key="bgCircle.id"
          :cx="bgCircle.cx"
          :cy="bgCircle.cy"
          :r="5"
          class="bg-circle"
        />

        <circle
          v-for="circle in circles"
          :id="circle.id"
          :key="circle.id"
          :cx="circle.cx"
          :cy="circle.cy"
          :r="5"
          class="circle"
        >
          <!-- <animate
            v-if="idx <= credits"
            :xlinkHref="circle.link"
            attributeName="cx"
            :from="circle.cx"
            :to="300"
            dur="2s"
            begin="1s"
            fill="freeze"
          />

          <animate
            v-if="idx <= credits"
            :xlinkHref="circle.link"
            attributeName="cy"
            :from="circle.cy"
            :to="150"
            dur="2s"
            begin="1s"
            fill="freeze"
          /> -->
        </circle>
      </g>
    </svg>
  </div>
</template>

<style lang="scss">
.circle {
  fill: #e1dfd0;
  z-index: 10;
}
.bg-circle {
  fill: #696966;
}
</style>
