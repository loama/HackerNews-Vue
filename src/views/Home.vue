<template>
  <div class="home">
    <div id="nav">
      <img class="logo" src="../assets/logo.png">

      <div id="view-selector">
        <div class="option">New</div>
        <div class="option">Top</div>
        <div class="option">Best</div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'home',
  components: {
  },
  mounted () {}
}
</script>

<style lang="sass">
  @import "@/variables.sass"

  #nav
    background: $orange
    height: 56px
    left: 0
    position: fixed
    top: 0
    width: 100vw

    .logo
      height: 40px
      margin: 8px
</style>
