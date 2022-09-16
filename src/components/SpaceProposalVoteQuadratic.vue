<script>
import { ref, watch } from 'vue';
import { percentageOfTotal } from '@snapshot-labs/snapshot.js/src/voting/quadratic';
import { useMediaQuery } from '@vueuse/core';

export default {
  // state
  data() {
    return {
      vote: 0,
      defaultColor: '#e1dfd0',
      negative: [
        '#FF3856',
        '#FF3856',
        '#FF5270',
        '#FF5270',
        '#FF6B89',
        '#FF6B89',
        '#FF85A3',
        '#FF85A3',
        '#FF9EBC',
        '#FF9EBC'
      ],
      positive: [
        '#0D8C31',
        '#0D8C31',
        '#27A64B',
        '#27A64B',
        '#40BF64',
        '#40BF64',
        '#5AD97E',
        '#5AD97E',
        '#73F297',
        '#73F297'
      ]
    };
  },

  methods: {
    getXYAnimation(index) {
      const circle = document.getElementById(`animated-${index}`);
      const diamond = document.getElementById(`diamond-${index}`);

      if (circle && diamond) {
        const rectA = circle.getBoundingClientRect();
        const rectB = diamond.getBoundingClientRect();

        return {
          x: rectB.left - rectA.left,
          y: rectB.top - rectA.top
        };
      }
    },
    negativeVote(from, to) {
      for (let index = 0; index < to; index++) {
        const elementIndex = from - (index + 1);
        const circle = document.getElementById(`animated-${elementIndex}`);

        circle.animate([{ transform: `translate(0px, 0px)`, fill: 'red' }], {
          duration: 1000,
          fill: 'both'
        });

        circle.animate(
          [
            {
              fill: this.defaultColor
            }
          ],
          {
            duration: 10,
            fill: 'both'
          }
        );
      }
    },
    positiveVote(from, to) {
      for (let index = from - 1; index >= to; index--) {
        const circle = document.getElementById(`animated-${index}`);
        const element = this.getXYAnimation(index);

        circle.animate(
          [{ transform: `translate(${element.x}px, ${element.y}px)` }],
          {
            duration: 1000,
            fill: 'both'
          }
        );

        console.log('vote', this.vote * -1 - 1);

        circle.animate(
          [
            {
              fill:
                this.vote > 0
                  ? this.positive[this.vote - 1]
                  : this.negative[this.vote * -1 - 1]
            }
          ],
          {
            duration: 500,
            delay: 500,
            fill: 'both'
          }
        );
      }
    },

    voteUp() {
      if (this.vote < 10) {
        const isPositive = this.vote >= 0;
        const isNegative = this.vote < 0;
        let prevCredits = this.vote * this.vote;
        this.vote++;
        let newCredits = this.vote * this.vote;

        let diff = prevCredits - newCredits;

        if (isPositive) {
          this.positiveVote(newCredits, prevCredits);
        } else if (isNegative) {
          this.negativeVote(prevCredits, diff);
        }
      }
    },

    voteDown() {
      if (this.vote > -10) {
        const isPositive = this.vote > 0;
        const isNegative = this.vote <= 0;
        let prevCredits = this.vote * this.vote;
        this.vote--;
        let newCredits = this.vote * this.vote;

        let diff = prevCredits - newCredits;

        if (isPositive) {
          this.negativeVote(prevCredits, diff);
        } else if (isNegative) {
          this.positiveVote(newCredits, prevCredits);
        }
      }
    }
  }
};

// Delete choice if empty string or 0
// watch(selectedChoices.value, currentValue => {
//   Object.entries(currentValue).forEach(choice => {
//     if (choice[1] === '' || choice[1] <= 0)
//       delete selectedChoices.value[choice[0]];
//   });
//   emit('selectChoice', selectedChoices.value);
// });
</script>

<template>
  <div class="flex">
    <div>
      <QuadraticVotePool :vote="vote" :credits="vote * vote" />
    </div>

    <div class="flex flex-1 items-center justify-center">
      <BaseButton
        class="flex items-center justify-between overflow-hidden"
        @click="voteUp()"
      >
        Agree
      </BaseButton>

      <div class="flex flex-col items-center">
        <div>cookies {{ vote }}</div>
        <QuadraticVoteDiamond />
      </div>

      <BaseButton
        class="flex items-center justify-between overflow-hidden"
        @click="voteDown()"
      >
        Disagree
      </BaseButton>
    </div>
  </div>
  <!-- <div class="mb-3">
    <div v-for="(choice, i) in proposal.choices" :key="i">
      <BaseButton
        class="mb-2 flex w-full items-center justify-between overflow-hidden"
        :class="selectedChoices[i + 1] > 0 && '!border-skin-link'"
      >
        <div
          v-tippy="{
            content: choice.length > 20 && isSmallScreen ? choice : null
          }"
          class="truncate pr-3 text-left"
        >
          {{ choice }}
        </div>
        <div class="flex items-center justify-end">
          <button
            :disabled="!selectedChoices[i + 1]"
            class="btn-choice"
            @click="removeVote(i + 1)"
          >
            -
          </button>
          <input
            v-if="!isSmallScreen"
            v-model.number="selectedChoices[i + 1]"
            class="input text-center"
            :class="{ 'btn-choice': isSmallScreen }"
            style="width: 40px; height: 44px"
            placeholder="0"
            type="number"
          />
          <div v-if="isSmallScreen" style="min-width: 56px">
            {{ percentage(i) }}%
          </div>
          <button class="btn-choice" @click="addVote(i + 1)">+</button>
          <div
            v-if="!isSmallScreen"
            style="min-width: 52px; margin-right: -5px"
            class="text-right"
          >
            {{ percentage(i) }}%
          </div>
        </div>
      </BaseButton>
    </div>
  </div> -->
</template>

<style lang="scss" scoped>
.btn-choice {
  background-color: transparent;
  color: var(--link-color);
  width: 40px;
  height: 44px;
  border-left: 1px solid var(--border-color);
  border-right: 1px solid var(--border-color);
  border-bottom: none;
  border-top: none;
  &:disabled {
    color: gray;
  }
}
</style>
