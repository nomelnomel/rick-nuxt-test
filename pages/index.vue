<template>
  <div class="container" v-if="chars">
    <div class="char-list">
      <nuxt-link
        v-for="char in chars"
        :key="char.id"
        class="char"
        :to="'/' + char.id"
      >
        <div class="char-image">
          <img :src="char.image" alt="">
        </div>
        <div class="char-info">
          <div class="char-main">
            <div class="char-name">
              {{ char.name }}
            </div>
            <div class="char-status">
              <span class="round" :class="getStatus(char.status)"></span>{{ char.status }} - {{ char.species }}
            </div>
          </div>
          <div class="char-location">
            <span>Last known location:</span>
            {{ char.location.name }}
          </div>
          <div class="char-episode">
            <span>First seen in:</span>
            {{char.episode.name}}
          </div>
        </div>
      </nuxt-link>
    </div>
    <infinite-loading
      spinner="spiral"
      @infinite="infiniteScroll"
    ></infinite-loading>
  </div>
  <div v-else>
    waiting..
  </div>
</template>

<script>
export default {
  data() {
    return {
      chars: null,
      page: 1,
      height: null,
      episode: null
    }
  },
  async fetch() {
    const response = await fetch(this.url)
    const chars = await response.json().then(res => res.results)
    const charsWithEpisode = chars.map(async item => {
      let id = item.episode[0].slice(-2)
      if(id[0] === '/') id = id.slice(-1)
      const response = await fetch(`https://rickandmortyapi.com/api/episode/${id}`)
      const episode = await response.json()
      return {
      ...item,
        episode
      }
    })
    this.chars = await Promise.all(charsWithEpisode)
  },
  methods: {
    async infiniteScroll($state) {
      this.page++
      await fetch(this.url)
        .then(res => res.json())
        .then(res => {
          if (res.results.length > 1) {
            this.chars.push(...res.results)
            // this.chars = this.chars.concat(res.results)
            $state.loaded()
          } else {
            $state.complete()
          }
        }).catch((err) => {
          console.log(err)
        })
    },
    getStatus(status) {
      return {
        dead: status.toLowerCase() === 'dead',
        alive: status.toLowerCase() === 'alive'

      }
    },
  computed: {
    url() {
      return 'https://rickandmortyapi.com/api/character?page=' + this.page
    },
    episodeName(){
     return this.episode.name
    }
  }
}
</script>

<style scoped lang="scss">
.char-list {
  display: grid;
  grid-gap: 25px;
  grid-template-columns: repeat(auto-fit, minmax(500px, 1fr));
}

.char {
  display: flex;
  text-decoration: none;
  color: white;
  overflow: hidden;
  border-radius: 10px;
}

.char-image {
  flex: 2 1 0;
  width: 100%;

  img {
    width: 100%;
    height: 100%;
    object-position: center center;
    object-fit: cover;
  }
}

.char-info {
  flex: 3 1 0;
  font-size: 20px;
  background-color: #3c3e44;
  padding: 10px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.char-main, .char-location, .char-episode {
  display: flex;
  flex-direction: column;

  span {
    color: #9e9e9e;
    font-size: 16px;
  }
}

.char-location, .char-episode {
  font-size: 20px;
}

.char-name {
  font-size: 26px;
  font-weight: bold;
}

.char-status {
  font-size: 16px;
  display: flex;
  align-items: center;
  text-transform: capitalize;
}

.round {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  display: inline-block;
  margin-right: 3px;
  background-color: #9e9e9e;
}

.dead {
  background-color: red;
}

.alive {
  background-color: greenyellow;
}


</style>
