<template>
  <div id="windows-container">
    <template v-for="windowInstance of windowInstances">
      <component
        :is="windowInstance.name"
        :data="windowInstance"
        :data-window-id="windowInstance.uniqueID"
        :key="windowInstance.uniqueID"
      />
    </template>
  </div>
</template>

<script>
import {mapGetters} from 'vuex'

export default {
  name: 'WindowsContainer',
  computed: {
    ...mapGetters({
      windowInstances: 'core/windows/windowInstances'
    })
  }
}
</script>

<style lang="scss">
#app {
  #windows-container {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    pointer-events: none;
    z-index: 10;

    .windows-group {
      min-height: 100%;
    }
  }

  &.is-mobile {
    #windows-container {
      top: 24px;
      bottom: 24px;
      left: 24px;
      right: 24px;

      @media(max-width: 560px) {
        top: 15px;
        bottom: 15px;
        left: 15px;
        right: 15px;
      }
    }
  }
}
</style>
