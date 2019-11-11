<template>
  <div class="home">
    <div id="nav">
      <img class="logo" src="../assets/logo.png">

      <div id="view-selector">

        <div class="option"
             v-on:click="selectView('new')">
             New
        </div>

        <div class="option"
             v-on:click="selectView('top')">
             Top
        </div>

        <div class="option"
             v-on:click="selectView('best')">
             Best
        </div>

        <div class="indicator"
             v-bind:class="activeView">{{activeView}}</div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  data: function () {
    return {
      activeView: 'new'
    }
  },
  name: 'home',
  components: {
  },
  methods: {
    selectView (view) {
      this.activeView = view
    }
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
      display: inline-block
      height: 40px
      margin: 8px

    #view-selector
      background: #FFFFFF
      display: inline-block
      border-radius: 2px
      height: 32px
      margin: 12px calc(50vw - 152px)
      position: relative
      vertical-align: top
      width: 192px

      .option
        background: #FFF
        color: #424242
        cursor: pointer
        display: inline-block
        height: 28px
        line-height: 28px
        margin: 2px
        text-align: center
        width: 60px

      .indicator
        background: $orange
        border-radius: 2px
        color: #FFF
        height: 28px
        left: 2px
        line-height: 28px
        position: absolute
        text-align: center
        text-transform: capitalize
        top: 2px
        transition: all 0.3s
        width: 60px

        &.new
          transform: translate3d(0, 0, 0)

        &.top
          transform: translate3d(64px, 0, 0)

        &.best
          transform: translate3d(128px, 0, 0)
</style>
