<template>
  <div class="PageFooter">
    <button @click="goToPage(prev)" :disabled="!isPrevActive" class="arrow-button">
      <i class="material-icons" :class="{'isArrowActive' : isPrevActive}" style="font-size: 30px;
      padding-top: 3px; color: #9d9b9b;">arrow_left</i></button>
    <button @click="goToPage(firstPage.firstPageNumber)" :disabled="firstPage.isFirstActive"
            class="page-button" :class="{'isActive' : firstPage.isFirstActive}">
      {{ firstPage.firstPageNumber }}
    </button>
    <button v-if="startHidden" class="page-button">...</button>
    <div v-for="(page, index) in pagesArray" :key="index" class="page-buttons">
      <button @click="goToPage(page.pageNumber)" :disabled="page.isActive"
              class="page-button" :class="{'isActive' : page.isActive}">
        {{ page.pageNumber }}
      </button>
    </div>
    <button v-if="endHidden" class="page-button">...</button>
    <button @click="goToPage(lastPage.lastPageNumber)" :disabled="lastPage.isLastActive"
            class="page-button" :class="{'isActive' : lastPage.isLastActive}">
      {{ lastPage.lastPageNumber }}
    </button>
    <button @click="goToPage(next)" :disabled="!isNextActive" class="arrow-button">
      <i class="material-icons" :class="{'isArrowActive' : isNextActive}" style="font-size: 30px;
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
      required: true,
    },
    prev: Number,
    next: Number,
    isPrevActive: Boolean,
    isNextActive: Boolean,
    firstPage: Object,
    lastPage: Object,
    startHidden: Boolean,
    endHidden: Boolean,
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
  display: flex;
  margin-bottom: 30px;
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

.isActive {
  background-color: #08B2C9;
  color: white;
  border-color: #08B2C9;
}

.isArrowActive {
  color: #08B2C9 !important;
}

</style>
