<template>
  <div>{{ count.length }}</div>
</template>

<script lang="ts">
import { ref } from "vue";

export default {
  setup() {
    const count = ref(0);
    return { count };
  }
};
</script>
