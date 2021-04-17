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
      <CharacterList v-if="!areFavVisible" :characterList="allCharacterList"
                     @addToFavorites="addToFavorites"
                     @removeFromFavorites="removeFromFavorites"/>
      <CharacterList v-if="areFavVisible" :characterList="favCharacterList"
                     @removeFromFavorites="removeFromFavorites"/>
    </div>
    <PageFooter :pagesArray="pagesArray" :areFavVisible="areFavVisible"
                :favListLength="favCharacterList.length" @goToPage="goToPage"/>
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
  episode: string
  isFav?: boolean
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
    episode: string
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
    let areFavVisible = ref(false)
    let pagesCount = ref(1)
    let rickApiCollection = reactive({}) as RickApiInterface
    let result = reactive({}) as CharacterInterface
    let allCharacterList = reactive([{} as CharacterInterface])
    let favCharacterList = reactive([{} as CharacterInterface])
    let page = ref(1)
    let isActive = ref(true)
    let pagesArray = reactive([{page, isActive}])

    allCharacterList.splice(0, 1)
    favCharacterList.splice(0, 1)
    pagesArray.splice(0, 1)

    const getRickApiCollection = async (page: number) => {
      rickApiCollection = await rickApi.getCharacter({page: page})
      pagesCount.value = rickApiCollection.info.pages
      for (let i = 0; i < pagesCount.value; i++) {
        if(i === 0){
          pagesArray[i].page = i + 1
          pagesArray[i].isActive = true
        } else {
          pagesArray[i].page = i + 1
          pagesArray[i].isActive = false
        }
      }
      console.log(pagesArray)
      for (result of rickApiCollection.results) {
        if (parseInt(result.episode[result.episode.length - 1].substring(40)) < 10) {
          result.episode = 's01e0' + result.episode[result.episode.length - 1].substring(40)
        } else if (parseInt(result.episode[result.episode.length - 1].substring(40)) < 12) {
          result.episode = 's01e' + result.episode[result.episode.length - 1].substring(40)
        } else if (parseInt(result.episode[result.episode.length - 1].substring(40)) < 21) {
          result.episode = 's02e0' + (parseInt(result.episode[result.episode.length - 1].substring(40)) - 11).toString()
        } else if (parseInt(result.episode[result.episode.length - 1].substring(40)) < 22) {
          result.episode = 's02e' + (parseInt(result.episode[result.episode.length - 1].substring(40)) - 11).toString()
        } else if (parseInt(result.episode[result.episode.length - 1].substring(40)) < 31) {
          result.episode = 's03e0' + (parseInt(result.episode[result.episode.length - 1].substring(40)) - 21).toString()
        } else if (parseInt(result.episode[result.episode.length - 1].substring(40)) < 32) {
          result.episode = 's03e' + (parseInt(result.episode[result.episode.length - 1].substring(40)) - 21).toString()
        } else if (parseInt(result.episode[result.episode.length - 1].substring(40)) < 41) {
          result.episode = 's04e0' + (parseInt(result.episode[result.episode.length - 1].substring(40)) - 31).toString()
        } else if (parseInt(result.episode[result.episode.length - 1].substring(40)) < 42) {
          result.episode = 's04e' + (parseInt(result.episode[result.episode.length - 1].substring(40)) - 31).toString()
        }
        allCharacterList.push({
          image: result.image,
          id: result.id,
          name: result.name,
          gender: result.gender,
          species: result.species,
          episode: result.episode,
          isFav: false,
        })
      }
    }
    const showAll = () => {
      areFavVisible.value = false
    }
    const showFav = () => {
      areFavVisible.value = true
    }
    const goToPage = (page: number) => {
      allCharacterList.splice(0, 20)
      getRickApiCollection(page)
    }

    onMounted(() => {
      getRickApiCollection(1)
    })

    return {
      areFavVisible,
      pagesCount,
      pagesArray,
      allCharacterList,
      favCharacterList,
      getRickApiCollection,
      showAll,
      showFav,
      goToPage,
    }
  },
  methods: {
    // wygląda to na bug vue3, komponent się nie rerenderuje i trzeba to wymuszać,
    // natomiast wewnątrz setup() nie można skorzystać z $forceUpdate
    addToFavorites(id: number) {
      for (let character of this.allCharacterList) {
        if (character.id === id) {
          character.isFav = true
          this.favCharacterList.push(character)
        }
      }
      this.$forceUpdate()
    },
    removeFromFavorites(id: number) {
      for (let character of this.allCharacterList) {
        if (character.id === id) {
          character.isFav = false
        }
      }
      this.favCharacterList = this.favCharacterList.filter(element => element.id !== id)
      this.$forceUpdate()
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
