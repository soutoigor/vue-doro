<template>
  <div class="actions">
    <button
      class="actions__button"
      @click="toggleModal"
    >
      <sliders-icon />
    </button>
    <button
      class="actions__button--toggle"
      @click="toggleIsPlaying"
    >
      <transition-group name="toggle-buttons">
        <pause-icon
          v-if="isPlaying"
          key="pause"
        />
        <play-icon
          v-else
          key="play"
        />
      </transition-group>
    </button>
    <button
      class="actions__button"
      @click="skipStep"
    >
      <skip-forward-icon />
    </button>
    <action-adjustments
      v-show="isShowingAdjustments"
      @close="toggleModal"
      @setAdjustments="setAdjustments"
    />
  </div>
</template>

<script>
import {
  PlayIcon,
  PauseIcon,
  SlidersIcon,
  SkipForwardIcon,
} from 'vue-feather-icons'

export default {
  components: {
    PlayIcon,
    PauseIcon,
    SlidersIcon,
    SkipForwardIcon,
    ActionAdjustments: () => import('@/components/ActionAdjustments'),
  },
  data() {
    return {
      isPlaying: false,
      isShowingAdjustments: false,
    }
  },
  methods: {
    setAdjustments(adjustments) {
      this.$emit('setAdjustments', adjustments)
    },
    toggleIsPlaying() {
      this.isPlaying = !this.isPlaying
      this.$emit('toggleIsPlaying', this.isPlaying)
    },
    skipStep() {
      this.$emit('skipStep')
    },
    toggleModal() {
      this.isShowingAdjustments = !this.isShowingAdjustments
    },
  },
}
</script>

<style lang="stylus" scoped>
.actions
  display flex
  justify-content space-evenly
  width 40%
  @media screen and (max-width $mobile)
    width 100%

.actions__button
  background-color transparent
  border none
  cursor pointer
  width 4rem
  height @width
  hoverTransition()
  border-radius 100%
  padding-top .5rem

.actions__button--toggle
  @extends .actions__button
  border 2px solid $border-color
  addShadow()

.toggle-buttons-enter-active
  transition all .2s linear

.toggle-buttons-enter, .toggle-buttons-leave-to
  transform translateY(5px)
  opacity 0
</style>
