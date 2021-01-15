<template>
  <div v-if="char" class="container">
    <nuxt-link to="/" class="back">
      <= Back
    </nuxt-link>
    <div class="char">
      <div class="char-image">
        <img :src="char.image" alt="">
      </div>
      <div class="char-docs">
        <div>
          <span>Name: </span>{{char.name}}
        </div>
        <div>
          <span>Gender: </span>{{char.gender}}
        </div>
        <div>
          <span>Location: </span>{{char.location.name}}
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    waiting...
  </div>
</template>

<script>
export default {
  data() {
    return {
      char: null
    }
  },
  async fetch() {
    this.char = await fetch(`https://rickandmortyapi.com/api/character/${this.$route.params.id}`)
    .then(res => res.json())
  }
}
</script>

<style scoped lang="scss">

.container{
  margin: 50px;
}
.char-card{
  display: flex;
  flex-direction: column;

}

.char-docs{
  display: flex;
  flex-direction: column;

  div{
    span{
      font-weight: bold;
    }
  }
}

.back{
  color: black;
  text-decoration: none;
  margin-bottom: 20px;
  border: 1px solid black;
  display: inline-block;
  padding: 5px;
}
</style>
