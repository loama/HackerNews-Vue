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

    <div id="filters">
    </div>

    <ul class="stories">
      <li v-for="story in stories" v-bind:key="story.title">
        {{story}}
      </li>
    </ul>
  </div>
</template>

<script>

export default {
  data: function () {
    return {
      activeView: 'new',
      stories: [
        {
          'by': 'aminecodes',
          'descendants': 291,
          'id': 21137553,
          'kids': [
            21138653,
            21138113,
            21138829,
            21138131,
            21142749,
            21138320,
            21137931,
            21139047,
            21139160,
            21137857,
            21142759,
            21139206,
            21137889,
            21138187,
            21139948,
            21137852,
            21139110,
            21139717,
            21139555,
            21138014,
            21137814,
            21139211,
            21137798,
            21139509,
            21142998,
            21146914,
            21138539,
            21140364,
            21139839,
            21138185,
            21139098,
            21138576,
            21138580,
            21138914,
            21140040,
            21139430,
            21137996,
            21138618,
            21137958,
            21137784,
            21139786
          ],
          'score': 372,
          'time': 1570030411,
          'title': 'Microsoft introduces Windows 10 X for dual-screen devices',
          'type': 'story',
          'url': 'https://techcrunch.com/2019/10/02/microsoft-introduces-windows-10-x-for-dual-screen-devices/'
        }
      ]
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

  .home
    margin-top: 64px

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

  ul.stories
    list-style: none
    margin: 0

    li
      border-bottom: 1px solid #D0D0D0
      list-style: none
</style>
