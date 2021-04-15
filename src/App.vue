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
      <CharacterList/>
    </div>
    <PageFooter/>
  </div>
</template>

<script lang="ts">
import {defineComponent} from 'vue';
import PageHeader from "@/components/PageHeader.vue";
import PageFooter from "@/components/PageFooter.vue";
import CharacterList from "@/components/CharacterList.vue";

export default defineComponent({
  name: 'App',
  components: {
    PageHeader,
    PageFooter,
    CharacterList,
  },
  data() {
    return {
      areFavVisible: false,
    }
  },
  /*props: {
    areFavVisible: {
      type: Boolean,
      default: false,
    },
  },*/
  methods: {
    showAll() {
      this.areFavVisible = false
    },
    showFav() {
      this.areFavVisible = true
    },
  }
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

button{
  font-size: 20px;
  outline: none;
  background-color: transparent;
  margin-top: 10px;
  margin-left: 20px;
}
.inactive{
  border: none;
  color: #9d9b9b;
}

.active{
  border: none;
  border-bottom: solid 5px #08B2C9;
  color: #08B2C9;
}
</style>
