<template>
  <div class="PageFooter">
    <h1> Footer?</h1>
  </div>
</template>

<script lang="ts">
import {defineComponent} from 'vue';

export default defineComponent({
  name: 'PageFooter',
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
