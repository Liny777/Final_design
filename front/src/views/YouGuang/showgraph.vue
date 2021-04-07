<template>
  <div v-cloak class="container">
    showgraph
  </div>
</template>
<script>
export default {
  name: 'Showgraph',
  data() {
    return {
      // activeIndex: '1',
      loginForm: {
        username: 'admin',
        password: '111111'
      },
      passwordType: 'password',
      capsTooltip: false,
      loading: false,
      showDialog: false,
      redirect: undefined,
      otherQuery: {}
    }
  },
  watch: {
  },
  created() {
    // window.addEventListener('storage', this.afterQRScan)
  },
  mounted() {
  },
  destroyed() {
    // window.removeEventListener('storage', this.afterQRScan)
  },
  methods: {
  }
}
</script>
<style scoped>
</style>
