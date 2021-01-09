<template>
  <div></div>
</template>

<script>
export default {
  name: "blank",
  created() {
    this.$store.dispatch('getNavList')
    localStorage.setItem('SET_NAV_ACTIVE', -1)
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>

</style>
