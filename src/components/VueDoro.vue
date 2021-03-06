<template>
  <div :class="actualStepClass">
    <app-header />
    <main class="page__main">
      <h2 class="main__step-title">{{ actualStep.title }}</h2>
      <vue-doro-timer
        :timer="actualStep.time"
        :is-playing="isPlaying"
        @timeFinished="setNextStep"
      />
      <span class="main__rounds">{{ actualRound }}/{{ rounds }} rounds</span>
      <vue-doro-actions
        @skipStep="setNextStep"
        @toggleIsPlaying="setIsPlaying"
        @setAdjustments="setAdjustments"
      />
    </main>
  </div>
</template>

<script>
import AppHeader from '@/components/AppHeader'
import VueDoroActions from '@/components/VueDoroActions'
import VueDoroTimer from '@/components/VueDoroTimer'
import timer from '@/enums/timer'

const {
  FOCUS,
  SHORT,
  LONG,
  ROUNDS,
} = timer

export default {
  components: {
    AppHeader,
    VueDoroActions,
    VueDoroTimer,
  },
  data() {
    return {
      actualRound: 1,
      rounds: ROUNDS,
      actualStep: null,
      isPlaying: false,
      steps: {
        focus: {
          title: FOCUS.LABEL,
          time: FOCUS.TIME,
        },
        shortBreak: {
          title: SHORT.LABEL,
          time: SHORT.TIME,
        },
        longBreak: {
          title: LONG.LABEL,
          time: LONG.TIME,
        },
      },
    }
  },
  created() {
    this.setActualStep(this.steps.focus)
  },
  computed: {
    actualStepClass() {
      const step = {
        [FOCUS.LABEL]: 'focus',
        [SHORT.LABEL]: 'short',
        [LONG.LABEL]: 'long',
      }
      return `page--${step[this.actualStep.title]}`
    },
  },
  methods: {
    setActualStep(value) {
      this.actualStep = value
    },
    setNextStep() {
      const { title } = this.actualStep
      if (title === LONG.LABEL) {
        this.setActualRound(1)
        this.setActualStep(this.steps.focus)
      } else if (title === FOCUS.LABEL && this.actualRound === this.rounds) {
        this.setActualStep(this.steps.longBreak)
      } else if (title === FOCUS.LABEL && this.actualRound !== this.rounds) {
        this.setActualStep(this.steps.shortBreak)
      } else if (title === SHORT.LABEL && this.actualRound !== this.rounds) {
        this.setActualStep(this.steps.focus)
        this.setActualRound(this.actualRound + 1)
      }
    },
    setIsPlaying(isPlaying) {
      this.isPlaying = isPlaying
    },
    setActualRound(value) {
      this.actualRound = value
    },
    setAdjustments(adjustments) {
      Object.entries(adjustments).forEach(([key, value]) => {
        if (key === 'rounds') {
          this.rounds = +value
        } else {
          this.steps[key].time = value
        }
      })
      this.setActualRound(1)
      this.setActualStep(this.steps.focus)
    },
  },
}
</script>

<style lang="stylus" scoped>
pageStatusGradient(endColor)
  background linear-gradient(180deg, #fff -8%, rgba(#fff, 0) 100%), endColor

.page
  display grid
  grid-row-grap .9rem
  grid-template-rows 6rem 1fr
  height 100vh
  padding 0 1.3rem

.page--focus
  @extends .page
  pageStatusGradient($cosmos)

.page--short
  @extends .page
  pageStatusGradient($pattens-blue)

.page--long
  @extends .page
  pageStatusGradient($tana)

.page__main
  display grid
  grid-row-gap 2rem
  grid-template-rows 4rem 50% 3rem 6rem
  justify-items center
  align-items center

.main__step-title
  font-size 2.1rem
  font-weight 400
  @media screen and (max-width $mobile)
    font-size 1.7rem

.main__rounds
  letter-spacing .1rem
</style>
