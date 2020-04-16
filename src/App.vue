<template>
  <div id="app">
    <header>
      <img src="./assets/images/dstv-logo.png" alt="" width="90">
      <p>Channels</p>
    </header>

    <article class="search">
      <input 
        type="text" 
        name="" 
        id="channel-input" 
        placeholder="natgeo" 
        v-model="searchTerm"
        @focus="jumpToTop"
      >
      <p class="caption">Search channel</p>
    </article>

    <article class="list">
      <ul>
        <Channel 
          v-for="channel in filtered"
          :channel="channel"
          :key="channel.id"
        > 
        </Channel>
      </ul>
    </article>
  </div>
</template>

<script>
import Channel from './components/Channel.vue'
import access from './assets/channels/access.json'
import compactPlus from './assets/channels/compact_plus.json'
import compact from './assets/channels/compact.json'
import family from './assets/channels/family.json'
import free from './assets/channels/free.json'
import premium from './assets/channels/premium.json'
import fuzzy from 'fuzzysort'

const channelNames = new Map([
  ['premium', 'DSTV Premium',],
  ['compact', 'DSTV Compact'],
  ['compact_plus', 'DSTV Compact Plus'],
  ['family', 'DSTV Family'],
  ['access', 'DSTV Access'],
  ['free', 'Free to View'],
])

const channelNamesArr = Array.from(channelNames.keys())

// console.log(access.JSON.items[0])
const jsons = [access, compactPlus, compact, family, free, premium]
const channels = jsons.map(json => json.JSON.items)
const withBouquet = channels.map((list, index) => list.map(channel => {
  channel.channelNumber = parseInt(channel.channelNumber)
  channel.bouquet = channelNamesArr[index]
  return channel
}))
.flat(1) // join lists
.reduce((acc, current) => { // remove duplicates
  // returns values with no match
  if (!acc.some(channel => channel.id === current.id)) acc.push(current)
  return acc
}, [])

export default {
  name: 'App',

  data() {
    return {
      channels: withBouquet,
      searchTerm: '',
      preparedTargets: undefined, // fuzzysort
    }
  },

  computed: {
    filtered() {
      if (this.searchTerm === '') return this.channels
      return fuzzy.go(this.searchTerm, this.channels, { key: 'channelName' }).map(result => result.obj)
      // return this.channels.filter(channel => channel.channelName.toLowerCase().includes(this.searchTerm.toLowerCase()))
    }
  },

  methods: {
    jumpToTop({ target }) {
      target.scrollIntoView({
        behavior: 'smooth',
      })
    }
  },

  mounted(){
    this.channels.forEach(channel => channel.filePrepared = fuzzy.prepare(channel.channelName))
  },

  components: {
    Channel,
  },
}
</script>

<style>
* {
  box-sizing: border-box;
}

body {
  padding: 0;
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  display: flex;
  flex-direction: column;
}

header {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1em .75em .5em .75em;
  /* margin-bottom: 1em; */
}

header > img {
  padding-right: 1em;
}

header > p {
  font-size: 1.5em;
  text-align: center;
  font-weight: bold;
  color: #0094d9;
  margin: 0;
}

.search > input {
  line-height: 2em;
  font-size: 1em;
  background: rgba(150, 150, 150, .1);
  padding: .5em 1em;
  border: none;
  border-bottom: 2px solid #0094d9;
  /* width: 100%; */
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
  width: 100%;
}

.search {
  background: white;
  position: sticky;
  top: 0;
  /* border: 1px solid red; */
  padding: .5em .5em .25em .5em;
  box-shadow: 0px 10px 10px 0px rgba(150, 150, 150, 0.3); /* x, y, blur, spread */
}

.caption {
  margin: 0;
  padding: .25em 0 0 0;
  font-size: .8em;
  color: rgba(150, 150, 150, .8);
  font-style: italic;
  text-align: right;
}

.list {
  padding: 0 0.5em;
}

.list > ul {
  list-style-type: none;
  padding-left: 0;
}
</style>
