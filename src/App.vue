<template lang="pug">
  .App
    main.AppContent
      router-view
</template>

<script>
  import { mapGetters } from 'vuex'
  import AppNavigation from '@/view/global/app-navigation'

  export default {
    name: 'App',
    components: {
      AppNavigation,
    },
    computed: {
      ...mapGetters('i18n', [
        'langDefault'
      ])
    },
    mounted () {
      const payload = {
        lang: this.langDefault,
        app: this
      }
      this.$store.commit('i18n/LANG_CHANGE', payload, { root: true })
    }
  }
</script>

<style lang="scss">
  @import "stylesheet/app";
</style>
