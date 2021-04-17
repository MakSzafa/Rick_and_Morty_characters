<template>
  <div class="PageFooter">
    <button class="arrow-button">
      <i class="material-icons" style="font-size: 30px;
      padding-top: 3px; color: #9d9b9b;">arrow_left</i></button>
    <div v-for="(page, index) in pagesArray" :key="index" class="page-buttons">
      <button @click="goToPage(page.page)" class="page-button" :class="{'isActive' : page.isActive}">
        {{ page.page }}</button>
    </div>
    <button class="arrow-button">
      <i class="material-icons" style="font-size: 30px;
      padding-top: 3px; color: #9d9b9b;">arrow_right</i></button>

  </div>
</template>

<script lang="ts">
import {defineComponent} from 'vue';

export default defineComponent({
  name: 'PageFooter',
  props: {
    pagesArray: {
      type: Array,
      required: false,
    },
    areFavVisible: {
      type: Boolean,
      required: true,
    },
    favListLength: {
      type: Number,
      required: false,
    },
  },
  methods: {
    goToPage(page: number) {
      this.$emit('goToPage', page)
    },
  }
});
</script>

<style scoped>
.PageFooter {
  padding: 0 50px;
}

.arrow-button {
  width: 40px;
  height: 40px;
  outline: none;
  border: solid 1px #9d9b9b;
  border-radius: 10px;
  margin: auto 5px;
  padding: 0;
}

.page-button {
  width: 40px;
  height: 40px;
  outline: none;
  border: solid 1px #9d9b9b;
  border-radius: 10px;
  margin: auto 5px;
  padding: 0;
  color: #9d9b9b;
}
.isActive{
  background-color: #08B2C9;
  color: white;
  border-color: #08B2C9;
}
.page-buttons {
  display: inline-block;
}
</style>
