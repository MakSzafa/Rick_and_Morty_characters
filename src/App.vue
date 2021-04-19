<template>
  <div id="app" class="MainPage">
    <PageHeader @search="handleInputSearch"/>
    <div class="content">
      <button :class="{ inactive: areFavVisible, active: !areFavVisible }"
              @click="showAll">All Characters
      </button>
      <button :class="{ inactive: !areFavVisible, active: areFavVisible }"
              @click="showFav">Favorites
      </button>
      <CharacterList v-if="!areFavVisible" :areSearchResults="areSearchResults" :characterList="allCharacterList"
                     :isFavListEmpty="false" @addToFavorites="addToFavorites"
                     @removeFromFavorites="removeFromFavorites"/>
      <CharacterList v-if="areFavVisible" :areSearchResults="true" :isFavListEmpty="isFavListEmpty"
                     :characterList="favCharacterList" @removeFromFavorites="removeFromFavorites"/>
    </div>
    <PageFooter v-if="!areFavVisible" :pagesArray="pagesArray" :areFavVisible="areFavVisible"
                :prev="prevPage" :isPrevActive="isPrevActive" :next="nextPage" :isNextActive="isNextActive"
                :firstPage="firstPage" :lastPage="lastPage" :isLastPage="isLastPage"
                :startHidden="startHidden" :endHidden="endHidden"
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
    prev: string
    next: string
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
    let rickApiCollection = reactive({}) as RickApiInterface
    let result = reactive({}) as CharacterInterface
    let allCharacterList = reactive([{} as CharacterInterface])
    let favCharacterList = reactive([{} as CharacterInterface])
    let areFavVisible = ref(false)
    let pagesCount = ref(1)
    let prevPage = ref(1)
    let isPrevActive = ref(false)
    let nextPage = ref(1)
    let isNextActive = ref(true)
    let pageNumber = ref(1)
    let isActive = ref(true)
    let pagesArray = reactive([{pageNumber, isActive}])
    let firstPageNumber = ref(1)
    let isFirstActive = ref(true)
    let firstPage = reactive({firstPageNumber, isFirstActive})
    let lastPageNumber = ref(1)
    let isLastActive = ref(true)
    let isLastPage = ref(true)
    let lastPage = reactive({lastPageNumber, isLastActive})
    let startHidden = ref(false)
    let endHidden = ref(true)
    let searchInput = ref('')
    let areSearchResults = ref(true)
    let isFavListEmpty = ref(true)

    allCharacterList.splice(0, 1)
    favCharacterList.splice(0, 1)
    pagesArray.splice(0, 1)

    const getRickApiCollection = async (page: number, input: string) => {
      try {
        rickApiCollection = await rickApi.getCharacter({page: page, name: input})
        pagesCount.value = rickApiCollection.info.pages

        firstPage.firstPageNumber = 1
        lastPage.lastPageNumber = pagesCount.value
        areSearchResults.value = true

        if (pagesArray.length === 0) {
          if (pagesCount.value === 1) {
            isLastPage.value = false
            firstPage.isFirstActive = true
            startHidden.value = false
            endHidden.value = false

          } else if (pagesCount.value < 8) {
            for (let i = 0; i < pagesCount.value - 2; i++) {
              pagesArray.push({
                pageNumber: i + 2,
                isActive: false,
              })
            }
            isLastPage.value = true
            firstPage.isFirstActive = true
            lastPage.isLastActive = false
            startHidden.value = false
            endHidden.value = false
          } else if (pagesCount.value > 7) {
            for (let i = 0; i < 4; i++) {
              pagesArray.push({
                pageNumber: i + 2,
                isActive: false,
              })
            }
            isLastPage.value = true
            firstPage.isFirstActive = true
            lastPage.isLastActive = false
            startHidden.value = false
            endHidden.value = true
          }
        } else if (pagesCount.value < 8 && page === 1) {
          firstPage.isFirstActive = true
          lastPage.isLastActive = false
          for (let i = 0; i < pagesArray.length; i++) {
            pagesArray[i].isActive = (i + 2) === page;
          }
        } else if (pagesCount.value < 8 && page === pagesCount.value) {
          firstPage.isFirstActive = false
          lastPage.isLastActive = true
          for (let i = 0; i < pagesArray.length; i++) {
            pagesArray[i].isActive = (i + 2) === page;
          }
        } else if (pagesCount.value < 8 && page !== 1 && page !== pagesCount.value) {
          firstPage.isFirstActive = false
          lastPage.isLastActive = false
          for (let i = 0; i < pagesArray.length; i++) {
            pagesArray[i].isActive = (i + 2) === page;
          }
        } else if (page === 1) {
          startHidden.value = false
          endHidden.value = true
          firstPage.isFirstActive = true
          lastPage.isLastActive = false
          pagesArray.splice(0, 4)
          for (let i = 0; i < 4; i++) {
            pagesArray.push({
              pageNumber: i + 2,
              isActive: false,
            })
          }
        } else if (page < 4) {
          startHidden.value = false
          firstPage.isFirstActive = false
          for (let i = 0; i < pagesArray.length; i++) {
            pagesArray[i].isActive = (i + 2) === page;
          }
        } else if (page < pagesCount.value - 4) {
          startHidden.value = true
          endHidden.value = true
          firstPage.isFirstActive = false
          lastPage.isLastActive = false
          pagesArray.splice(0, 4)
          pagesArray.push({
            pageNumber: page,
            isActive: true,
          })
          pagesArray.push({
            pageNumber: page + 1,
            isActive: false,
          })
          pagesArray.push({
            pageNumber: page + 2,
            isActive: false,
          })
        } else if (page < pagesCount.value) {
          startHidden.value = true
          endHidden.value = false
          firstPage.isFirstActive = false
          lastPage.isLastActive = false
          pagesArray.splice(0, 4)
          for (let i = pagesCount.value - 4; i < pagesCount.value; i++) {
            pagesArray.push({
              pageNumber: i,
              isActive: false,
            })
          }
          for (let i = 0; i < pagesArray.length; i++) {
            pagesArray[i].isActive = (i + pagesCount.value - 4) === page;
          }
        } else if (page === pagesCount.value) {
          startHidden.value = true
          endHidden.value = false
          firstPage.isFirstActive = false
          lastPage.isLastActive = true
          pagesArray.splice(0, 4)
          for (let i = 0; i < 4; i++) {
            pagesArray.push({
              pageNumber: i + pagesCount.value - 4,
              isActive: false,
            })
          }
        }

        if (rickApiCollection.info.prev == null) {
          isPrevActive.value = false
        } else {
          isPrevActive.value = true
          prevPage.value = parseInt(rickApiCollection.info.prev.substring(48))
        }
        if (rickApiCollection.info.next == null) {
          isNextActive.value = false
        } else {
          isNextActive.value = true
          nextPage.value = parseInt(rickApiCollection.info.next.substring(48))
        }
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
      } catch (e) {
        startHidden.value = false
        endHidden.value = false
        isLastPage.value = false
        isPrevActive.value = false
        isNextActive.value = false
        areSearchResults.value = false
      }
    }

    const showAll = () => {
      areFavVisible.value = false
      isFavListEmpty.value = false
    }
    const showFav = () => {
      areFavVisible.value = true
      isFavListEmpty.value = favCharacterList.length === 0;
    }
    const goToPage = async (page: number) => {
      allCharacterList.splice(0, 20)
      await getRickApiCollection(page, searchInput.value)
      for (let favCharacter of favCharacterList) {
        for (let character of allCharacterList) {
          if (character.id === favCharacter.id) {
            character.isFav = true
          }
        }
      }
    }
    const handleInputSearch = async (input: string) => {
      searchInput.value = input
      allCharacterList.splice(0, 20)
      pagesArray.splice(0, 34)
      await getRickApiCollection(1, input)
      for (let favCharacter of favCharacterList) {
        for (let character of allCharacterList) {
          if (character.id === favCharacter.id) {
            character.isFav = true
          }
        }
      }
    }
    onMounted(() => {
      getRickApiCollection(1, '')
    })

    return {
      areFavVisible,
      pagesArray,
      firstPage,
      lastPage,
      prevPage,
      nextPage,
      isPrevActive,
      isNextActive,
      startHidden,
      endHidden,
      isLastPage,
      allCharacterList,
      favCharacterList,
      areSearchResults,
      isFavListEmpty,
      getRickApiCollection,
      showAll,
      showFav,
      goToPage,
      handleInputSearch,
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
      this.favCharacterList.sort((a, b) => (a.id > b.id) ? 1 : ((b.id > a.id) ? -1 : 0))
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
