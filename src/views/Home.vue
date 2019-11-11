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
      <li v-for="(story, n) in filteredStories" v-bind:key="story.title">
        <div class="storyNumber">{{n + 1}}</div>
        <div class="story">
          <div class="title">{{story.title}}</div>
          <div class="details">
            <span>{{story.score}} points by {{story.by}} {{story.minutesAgo}} minutes ago</span>
            <span class="source">{{story.urlShortened}}</span>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  computed: {
    filteredStories () {
      return this.stories[this.activeView]
    }
  },
  data: function () {
    return {
      activeView: 'new',
      stories: {
        new: [],
        top: [],
        best: []
      }
    }
  },
  name: 'home',
  components: {
  },
  methods: {
    selectView (view) {
      this.activeView = view
    },
    getStory: async function (id, section) {
      try {
        const response = await axios.get('https://hacker-news.firebaseio.com/v0/item/' + String(id) + '.json?print=pretty')
        var story = response.data

        var timeSeconds = Math.round((new Date()).getTime() / 1000)
        var storyMinutesAgo = Math.round((timeSeconds - story.time) / 60)

        story.minutesAgo = storyMinutesAgo

        // console.log(storyMinutesAgo)

        if (story.url !== undefined) {
          var urlParts = story.url.split('/')
          story.urlShortened = urlParts[2]
        } else {
          story.urlShortened = undefined
        }

        switch (section) {
          case 'newstories':
            this.stories.new.push(story)
            break

          case 'topstories':
            this.stories.top.push(story)
            break

          case 'beststories':
            this.stories.best.push(story)
            break
        }
      } catch (error) {
        console.error(error)
      }
    }
  },
  mounted () {
    var self = this

    // var origins = ['newstories', 'topstories', 'beststories']

    axios.get('https://hacker-news.firebaseio.com/v0/' + 'newstories' + '.json?print=pretty')
      .then(function (response) {
        // handle success
        for (let j = 0; j < 10/* response.data.length */; j++) {
          self.getStory(response.data[j], 'newstories')
        }
      })
      .catch(function (error) {
        // handle error
        console.log(error)
      })

    axios.get('https://hacker-news.firebaseio.com/v0/' + 'topstories' + '.json?print=pretty')
      .then(function (response) {
        // handle success
        for (let j = 0; j < 10/* response.data.length */; j++) {
          self.getStory(response.data[j], 'topstories')
        }
      })
      .catch(function (error) {
        // handle error
        console.log(error)
      })

    axios.get('https://hacker-news.firebaseio.com/v0/' + 'beststories' + '.json?print=pretty')
      .then(function (response) {
        // handle success
        for (let j = 0; j < 10/* response.data.length */; j++) {
          self.getStory(response.data[j], 'beststories')
        }
      })
      .catch(function (error) {
        // handle error
        console.log(error)
      })
  }
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
      box-sizing: content-box
      list-style: none

      &:hover
        background: #F0F0F0
        cursor: pointer

      .storyNumber
        display: inline-block
        height: 55px
        line-height: 55px
        text-align: center
        vertical-align: top
        width: 40px

      .story
        display: inline-block
        width: calc(100% - 56px)

      .details
        .source
          float: right
</style>
