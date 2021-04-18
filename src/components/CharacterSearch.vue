<template>
  <div class="CharacterSearch">
    <input placeholder="Start typing to search..."
           type="text"
           @input="inputChanged"
    />
    <i class="material-icons" style="font-size: 30px; color: #08B2C9; padding-right: 10px;">search</i>
  </div>
</template>

<script lang="ts">
import {defineComponent} from 'vue';

const DEBOUNCE_TIMEOUT: number = 200

export default defineComponent({
  name: 'CharacterSearch',
  props: {
    isLoading: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      debounceTimeout: 0,
    }
  },
  methods: {
    inputChanged(event: Event) {
      const target = event.target as HTMLInputElement

      if (this.debounceTimeout) {
        clearTimeout(this.debounceTimeout)
      }

      this.debounceTimeout = setTimeout(
          () => this.$emit('search', target.value),
          DEBOUNCE_TIMEOUT
      )
    },
  }
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

</style>
