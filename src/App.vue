<template>
  <rock-paper-scissors />
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
import RockPaperScissors from "@/components/RockPaperScissors.vue";

@Options({
  components: {
    RockPaperScissors
  }
})
export default class App extends Vue {}
</script>

<style>
* {
  box-sizing: border-box;
}

:root {
  --color-white: #ffffff;
  --color-blue-5: hsl(229, 64%, 46%);
  --color-gray-7: hsl(229, 25%, 31%);
  --color-gray-3: hsl(217, 16%, 45%);
  --color-gray-1: hsl(229, 25%, 95%);

  --font-size-sm: 0.75rem;
  --font-size: 1rem;
  --font-size-md: 1.25rem;
  --font-size-lg: 1.75rem;
  --font-size-xl: 2.5rem;
  --font-size-xxl: 3rem;
  --font-size-xxxl: 3.5rem;

  --letter-spacing: 1px;
  --letter-spacing-loose: 2px;

  --font-weight: 600;
  --font-weight-bold: 700;

  --container-width: 800px;

  --border-radius-sm: 4px;
  --border-radius-md: 6px;
  --border-radius-lg: 8px;
  --border-radius-xl: 16px;
  --border-radius-circle: 100%;
}

body {
  margin: 0;
  font-size: 16px;
  font-family: "Barlow Semi Condensed", sans-serif;
  font-weight: var(--font-weight);
}

button {
  cursor: pointer;
}
</style>
