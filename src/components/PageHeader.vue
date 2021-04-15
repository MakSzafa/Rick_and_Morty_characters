<template>
  <div class="PageHeader">
    <img alt="Rick and Morty logo" src="../assets/logo.png">
    <CharacterSearch/>
  </div>
</template>

<script lang="ts">
import {defineComponent} from 'vue';
import CharacterSearch from "@/components/CharacterSearch.vue";

export default defineComponent({
  name: 'PageHeader',
  components: {
    CharacterSearch,
  },
});
</script>

<style scoped>

.PageHeader {
  display: grid;
  grid-template-columns: 350px 1fr;
  border-bottom: solid 1px #9d9b9b;
  padding: 30px 50px;
}

img {
  width: 250px;
  height: 73px;
}
</style>
