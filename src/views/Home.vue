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

      <div class="ordering"
           v-bind:class="{active: orderingVisible}"
           v-on:click="toggleVisibleOrder()">
        <img class="open"
             src="../assets/order.svg">

        <img class="close"
             src="../assets/delete.svg">
      </div>
    </div>

    <div id="ordering-modal"
         v-bind:class="{active: orderingVisible}"
         v-on:click="closeVisibleOrder()"></div>

    <div id="search">
      <img class="icon" src="../assets/search.svg">
      <input type="text"
             placeholder="Search by title, author or url..."
             v-model="searchQuery">
    </div>

    <div id="filters"
         v-bind:class="{active: orderingVisible}">

      <div class="title"> Order stories by </div>

      <div class="option"
           v-bind:class="{active: orderOption === 'score'}"
           v-on:click="selectOrderOption('score')">
        <div class="radio"></div>
        <div class="inside-radio"></div>
        <span> Score </span>
      </div>

      <div class="option"
           v-bind:class="{active: orderOption === 'title'}"
           v-on:click="selectOrderOption('title')">
        <div class="radio"></div>
        <div class="inside-radio"></div>
        <span> Title </span>
      </div>

      <div class="option"
        v-bind:class="{active: orderOption === 'posted'}"
        v-on:click="selectOrderOption('posted')">
        <div class="radio"></div>
        <div class="inside-radio"></div>
        <span> Posted </span>
      </div>

      <div class="ordering">

        <div class="ascending"
             v-on:click="moveOrdering('ascending')">
             Ascending
        </div>

        <div class="descending"
             v-on:click="moveOrdering('descending')">
             Descending
        </div>

        <div class="indicator"
             v-bind:class="ordering">{{ordering}}</div>
      </div>

    </div>

    <ul class="stories">
      <a :href="story.url"
         target="_blank"
         v-for="story in filteredStories"
         v-bind:key="story.title">
        <div class="storyNumber">{{story.number + 1}}</div>
        <div class="story">
          <div class="title">{{story.title}}</div>
          <div class="details">
            <span>{{story.score}} points by {{story.by}} {{story.minutesAgo}} minutes ago</span>
            <span class="source">{{story.urlShortened}}</span>
          </div>
        </div>
      </a>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  computed: {
    filteredStories () {
      var currentStories = this.stories[this.activeView]

      var storiesFiltered = []
      // search
      for (var i = 0; i < currentStories.length; i++) {
        var hasTitle = currentStories[i].title.toLowerCase().indexOf(this.searchQuery.toLowerCase()) !== -1
        var hasAuthor = currentStories[i].by.toLowerCase().indexOf(this.searchQuery.toLowerCase()) !== -1

        var hasUrl = currentStories[i].urlShortened.toLowerCase().indexOf(this.searchQuery.toLowerCase()) !== -1

        if (hasTitle || hasAuthor || hasUrl) {
          storiesFiltered.push(currentStories[i])
        }
      }

      var storiesOrdered

      if (this.ordering === 'ascending') {
        switch (this.orderOption) {
          case 'score':
            storiesOrdered = storiesFiltered.sort(function (a, b) { return a.score - b.score })
            break

          case 'title':
            storiesOrdered = storiesFiltered.sort(function (a, b) {
              if (a.title < b.title) { return -1 }
              if (a.title > b.title) { return 1 }
              return 0
            })
            break

          case 'posted':
            storiesOrdered = storiesFiltered.sort(function (a, b) { return a.minutesAgo - b.minutesAgo })
            break
        }
      } else {
        switch (this.orderOption) {
          case 'score':
            storiesOrdered = storiesFiltered.sort(function (a, b) { return b.score - a.score })
            break

          case 'title':
            storiesOrdered = storiesFiltered.sort(function (a, b) {
              if (a.title < b.title) { return 1 }
              if (a.title > b.title) { return -1 }
              return 0
            })
            break

          case 'posted':
            storiesOrdered = storiesFiltered.sort(function (a, b) { return b.minutesAgo - a.minutesAgo })
            break
        }
      }

      return storiesOrdered
    }
  },
  data: function () {
    return {
      activeView: 'new',
      orderingVisible: false,
      ordering: 'ascending',
      orderOption: 'posted',
      searchQuery: '',
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
    selectOrderOption (option) {
      this.orderOption = option
    },
    getStory: async function (n, id, section) {
      try {
        const response = await axios.get('https://hacker-news.firebaseio.com/v0/item/' + String(id) + '.json?print=pretty')
        var story = response.data

        story.number = n

        var timeSeconds = Math.round((new Date()).getTime() / 1000)
        var storyMinutesAgo = Math.round((timeSeconds - story.time) / 60)

        story.minutesAgo = storyMinutesAgo

        // console.log(storyMinutesAgo)

        if (story.url !== undefined) {
          var urlParts = story.url.split('/')
          story.urlShortened = urlParts[2]
        } else {
          story.url = 'https://news.ycombinator.com/item?id=' + String(story.id)
          story.urlShortened = 'news.ycombinator.com'
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
        // console.error(error)
      }
    },
    moveOrdering (ordering) {
      this.ordering = ordering
    },
    toggleVisibleOrder () {
      this.orderingVisible = !this.orderingVisible
    },
    closeVisibleOrder () {
      this.orderingVisible = false
    }
  },
  mounted () {
    var self = this

    // var origins = ['newstories', 'topstories', 'beststories']

    axios.get('https://hacker-news.firebaseio.com/v0/' + 'newstories' + '.json?print=pretty')
      .then(function (response) {
        // handle success
        for (let j = 0; j < 30/* response.data.length */; j++) {
          self.getStory(j, response.data[j], 'newstories')
        }
      })
      .catch(function (error) {
        // handle error
        alert('we had an error with hn api', error)
        // console.log(error)
      })

    axios.get('https://hacker-news.firebaseio.com/v0/' + 'topstories' + '.json?print=pretty')
      .then(function (response) {
        // handle success
        for (let j = 0; j < 30/* response.data.length */; j++) {
          self.getStory(j, response.data[j], 'topstories')
        }
      })
      .catch(function (error) {
        // handle error
        alert('we had an error with hn api', error)
      })

    axios.get('https://hacker-news.firebaseio.com/v0/' + 'beststories' + '.json?print=pretty')
      .then(function (response) {
        // handle success
        for (let j = 0; j < 30/* response.data.length */; j++) {
          self.getStory(j, response.data[j], 'beststories')
        }
      })
      .catch(function (error) {
        // handle error
        alert('we had an error with hn api', error)
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
    z-index: 10

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

    .ordering
      display: inline-block
      height: 56px
      position: relative
      vertical-align: top
      width: 56px

      img
        height: 24px
        margin: 16px
        position: absolute
        transition: all 0.3s

        &.open
          opacity: 1

        &.close
          opacity: 0
          pointer-events: none

      &.active
        img.open
          opacity: 0

        img.close
          opacity: 1

  #ordering-modal
    background: #D8D8D8
    height: 100vh
    left: 0
    opacity: 0
    pointer-events: none
    position: fixed
    top: 0
    transition: all 0.3s
    width: 100vw
    z-index: 9

    &.active
      opacity: 0.9
      pointer-events: all

  #filters
    background: #FFF
    color: #D0D0D0
    box-shadow: 0 1px 3px #888
    left: 0
    padding-left: 16px
    position: fixed
    top: -208px
    transition: all 0.3s
    height: 192px
    width: calc(100% - 16px)
    z-index: 9

    &.active
      transform: translate3d(0, 256px, 0)

    .title
      height: 40px
      line-height: 40px
      margin-top: 8px

    .option
      color: #424242
      cursor: pointer
      height: 32px
      line-height: 24px
      position: relative

      .radio
        border: 1px solid #999
        border-radius: 50%
        height: 20px
        position: absolute
        width: 20px

      .inside-radio
        background: $orange
        border-radius: 50%
        display: none
        height: 16px
        left: 3px
        position: absolute
        top: 3px
        width: 16px

      span
        margin-left: 32px

      &.active
        .inside-radio
          display: block

    .ordering
      border: 1px solid #D0D0D0
      border-radius: 2px
      color: #424242
      margin: 0 auto
      position: relative
      width: 168px

      .ascending
        cursor: pointer
        display: inline-block
        font-size: 14px
        height: 32px
        line-height: 34px
        text-align: center
        width: 50%

      .descending
        cursor: pointer
        display: inline-block
        font-size: 14px
        height: 32px
        line-height: 32px
        text-align: center
        width: 50%

      .indicator
        background: $orange
        border-radius: 2px
        color: #FFF
        font-size: 14px
        left: 2px
        line-height: 32px
        height: 30px
        position: absolute
        text-align: center
        text-transform: capitalize
        top: 2px
        transition: all 0.3s
        width: 84px

        &.ascending
          transform: translate3d(0, 0, 0)

        &.descending
          transform: translate3d(80px, 0, 0)

  #search
    margin: 8px auto
    position: relative
    width: 384px

    .icon
      height: 20px
      left: 10px
      position: absolute
      top: 8px

    input
      background: #F0F0F0
      border: 1px solid #D0D0D0
      border-radius: 2px
      font-size: 14px
      height: 32px
      line-height: 20px
      padding-left: 40px
      width: calc(100% - 40px)

      &::placeholder
        color: #999
        font-size: 14px

  ul.stories
    list-style: none
    margin: 0
    padding: 0
    width: 100%

    a
      border-bottom: 1px solid #D0D0D0
      box-sizing: content-box
      color: #424242
      display: block
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

        .title
          color: #424242
          font-weight: 500
          margin-top: 10px
          max-width: 100%
          overflow-x: hidden
          text-overflow: ellipsis
          white-space: nowrap

        .details
          color: #999999
          font-size: 12px

          .source
            float: right

  @media screen and (min-width: 560px)
    ul.stories
      margin: 0 auto
      width: 552px

  @media screen and (min-width: 992px)

    #ordering-modal
      display: none

    #nav
      .ordering
        display: none

    #filters
      box-shadow: none
      left: unset
      top: 56px
      right: 8px
      width: 132px

      .ordering
        margin: 8px 0
        width: 88px

        .ascending
          width: 100%

        .descending
          width: 100%

        .indicator
          width: 84px

          &.ascending
            transform: translate3d(0, 0, 0)

          &.descending
            transform: translate3d(0, 30px, 0)

    ul.stories
      margin: 0 auto
      width: 640px
</style>
