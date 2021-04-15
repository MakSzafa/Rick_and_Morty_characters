<template>
  <div class="CharacterList">
    <h1> Character list</h1>
  </div>
</template>

<script lang="ts">
import {defineComponent} from 'vue';

export default defineComponent({
  name: 'CharacterList',
  props: {
    msg: {
      type: String,
      required: false,
    },
  },
});
</script>

<style scoped>

</style>
