<template>
  <div id="app" class="MainPage">
    <PageHeader/>
    <div class="content">
      <button :class="{ inactive: areFavVisible, active: !areFavVisible }"
              @click="showAll">All Characters
      </button>
      <button :class="{ inactive: !areFavVisible, active: areFavVisible }"
              @click="showFav">Favorites
      </button>
      <CharacterList v-if="!areFavVisible" :characterList="allCharacterList" :characterFav="false"
                     @addToFavorites="addToFavorites"/>
      <CharacterList v-if="areFavVisible" :characterList="favCharacterList" :characterFav="true"
                     @removeFromFavorites="removeFromFavorites"/>
    </div>
    <PageFooter/>
  </div>
</template>

<script lang="ts">
import {defineComponent, onMounted, reactive, ref} from 'vue';
import PageHeader from "@/components/PageHeader.vue";
import PageFooter from "@/components/PageFooter.vue";
import CharacterList from "@/components/CharacterList.vue";

const rickApi = require('rickmortyapi')

interface CharacterInterface {
  image: string
  id: number
  name: string
  gender: string
  species: string
  episode: string[]
}

interface RickApiInterface {
  info: {
    pages: number
  }
  results: [{
    image: string
    id: number
    name: string
    gender: string
    species: string
    episode: string[]
  }]
}

export default defineComponent({
  name: 'App',
  components: {
    PageHeader,
    PageFooter,
    CharacterList,
  },
  setup() {
    let rickApiCollection = reactive({}) as RickApiInterface
    let pagesCount = ref(1)
    let result = reactive({}) as CharacterInterface
    let allCharacterList = reactive([{} as CharacterInterface])
    let favCharacterList = reactive([{} as CharacterInterface])

    allCharacterList.splice(0, 1)
    favCharacterList.splice(0, 1)

    const getRickApiCollection = async (page: number) => {
      rickApiCollection = await rickApi.getCharacter({page: page})
      pagesCount.value = rickApiCollection.info.pages
      for (result of rickApiCollection.results) {
        allCharacterList.push({
          image: result.image,
          id: result.id,
          name: result.name,
          gender: result.gender,
          species: result.species,
          episode: result.episode,
        })
      }
    }

    onMounted(() => {
      getRickApiCollection(1)
    })

    return {
      getRickApiCollection,
      pagesCount,
      allCharacterList,
      favCharacterList
    }
  },
  data() {
    return {
      areFavVisible: false,
    }
  },
  methods: {
    goToPage(page: number) {
      this.allCharacterList.splice(0, 20)
      this.getRickApiCollection(page)
    },
    addToFavorites(id: number) {
      for (let character of this.allCharacterList) {
        if (character.id === id) {
          this.favCharacterList.push(character)
        }
      }
      console.log(id)
      console.log(this.favCharacterList)
    },
    removeFromFavorites(id: number){
      console.log(id)
      this.favCharacterList.filter(element => element.id !== id)
      console.log(this.favCharacterList)
    },
    showAll() {
      this.areFavVisible = false
    },
    showFav() {
      this.areFavVisible = true
    },
  },
});
</script>

<style>
body {
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  display: grid;
  grid-template-columns: 1fr;
  grid-template-rows: auto 1fr auto;
  grid-template-areas: 'header'
            'content'
            'footer';
  min-height: 100vh;
}

.PageHeader {
  grid-area: header;
}

.PageFooter {
  grid-area: footer;
}

.content {
  grid-area: content;
  padding: 20px 50px;
}

button {
  font-size: 20px;
  outline: none;
  background-color: transparent;
  margin-top: 10px;
  margin-left: 20px;
}

.inactive {
  border: none;
  color: #9d9b9b;
}

.active {
  border: none;
  border-bottom: solid 5px #08B2C9;
  color: #08B2C9;
}
</style>
