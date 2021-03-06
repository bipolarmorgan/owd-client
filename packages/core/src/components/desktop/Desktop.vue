<template>
  <div id="desktop">
    <SystemBar v-if="systemBar.enabled" :system-bar="systemBar">
      <template v-slot:system-bar-status-prepend>
        <slot name="system-bar-status-prepend" />
      </template>
      <template v-slot:system-bar-status-append>
        <slot name="system-bar-status-append" />
      </template>
    </SystemBar>

    <div class="desktop-content">
      <slot />
    </div>
  </div>
</template>

<script>
  import SystemBar from "./system-bar/SystemBar";
  import mixinServer from "../../mixins/mixinServer";
  export default {
    name: "Desktop",
    mixins: [mixinServer],
    components: {SystemBar},
    props: {
      systemBar: {
        type: Object,
        default: {
          enabled: false,
          dark: true
        }
      }
    },
    beforeMount() {
      const self = this

      // redirect to homepage on 404
      if (!this.$route.name) {
        this.$router.push({ name: 'index' })
      }

      // on page ready, connect to SSE
      if (this.isServerAvailable) {
        window.addEventListener('load', function() {
          self.$store.dispatch('core/sse/connect', 'once')
        })
      }
    },
    created() {
      const self = this

      // initialize client
      this.$store.dispatch('core/client/initialize')

      // add window resize event
      window.addEventListener('resize', function () {
        clearTimeout(this.handlePageResize)

        this.handlePageResize = setTimeout(() => {
          self.$store.commit('core/windows/SET_DESKTOP_WIDTH', window.innerWidth)
          self.$store.commit('core/windows/SET_DESKTOP_HEIGHT', window.innerHeight)

          // windows position
          self.$store.dispatch('core/windows/windowsHandlePageResize')
        }, 100)
      })
    },
    destroyed() {
      const self = this

      // remove window resize event
      window.removeEventListener('resize', function () {
        self.$store.dispatch('core/windows/windowsHandlePageResize')
      })
    }
  }
</script>

<style scoped lang="scss">
  #desktop {
    display: flex;
    flex-flow: column;
    height: 100vh;

    .desktop-content {
      position: relative;
      flex: 1;
    }
  }
</style>