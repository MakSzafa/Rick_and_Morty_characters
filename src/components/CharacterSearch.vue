<template>
  <div class="CharacterSearch">
    <input :isLoading="isLoading"
           placeholder="Start typing to search..."
           type="text"
    />
    <div>
      <div :class="{ disabled: !isLoading, visible: isLoading }"
           class="loader">
      </div>
      <i class="material-icons" :class="{ disabled: isLoading, visible: !isLoading }"
         style="font-size: 30px; color: #08B2C9; padding-right: 10px;">search</i>
    </div>
  </div>
</template>

<script lang="ts">
import {defineComponent} from 'vue';

export default defineComponent({
  name: 'CharacterSearch',
  props: {
    isLoading: {
      type: Boolean,
      default: false,
    },
  },
});
</script>

<style scoped>

.CharacterSearch {
  display: flex;
  align-items: center;
  justify-content: space-between;
  border: solid 1px #807e7e;
  border-radius: 10px;
  height: 50px;
  margin-top: auto;
  margin-bottom: auto;
  max-width: 400px;
  color: #9d9b9b;
}

input {
  font-size: 20px;
  padding-left: 10px;
  border: none;
  outline: none;
}

.visible {
  display: block;
}

.disabled {
  display: none;
}
</style>
