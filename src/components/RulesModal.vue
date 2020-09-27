<template>
  <div v-if="isOpen" class="modal-container">
    <div @click="handleClose" class="backdrop"></div>
    <div class="modal">
      <div class="header">
        <h2 class="title">Rules</h2>
        <button
          type="button"
          class="close-button close-button--header"
          @click="handleClose"
        >
          <close-icon class="close-icon" />
        </button>
      </div>
      <rules-image class="image" />

      <div class="attribution">
        Challenge by
        <a href="https://www.frontendmentor.io?ref=challenge" target="_blank">
          Frontend Mentor</a
        >.<br />Coded by
        <a href="https://github.com/JoosepAlviste/">Joosep Alviste</a>.
      </div>

      <button
        type="button"
        class="close-button close-button--footer"
        @click="handleClose"
      >
        <close-icon class="close-icon" />
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import RulesImage from "@/assets/image-rules.svg?inline";
import CloseIcon from "@/assets/icon-close.svg?inline";

export default defineComponent({
  components: { RulesImage, CloseIcon },

  props: {
    isOpen: {
      type: Boolean
    }
  },

  emits: {
    close: () => true
  },

  setup(props, ctx) {
    const handleClose = () => {
      ctx.emit("close");
    };

    return { handleClose };
  }
});
</script>

<style scoped>
.modal-container {
  display: flex;
  align-items: center;
  justify-content: center;
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}

.backdrop {
  position: fixed;
  background-color: rgba(0, 0, 0, 0.5);
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: 1;
}

@media screen and (max-width: 400px) {
  .backdrop {
    display: none;
  }
}

.modal {
  position: relative;
  z-index: 2;
  background-color: var(--color-white);
  border-radius: var(--border-radius-lg);
  padding: 24px 32px;
}

@media screen and (max-width: 400px) {
  .modal {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-around;
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    border-radius: 0;
  }
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 32px;
  color: var(--color-gray-7);
}

.title {
  font-size: var(--font-size-lg);
  text-transform: uppercase;
  margin: 0;
}

.close-button {
  background: transparent;
  border: none;
  padding: 8px;
  transition: background-color 150ms ease-in-out;
  border-radius: var(--border-radius-sm);
}

.close-button:hover {
  background-color: var(--color-gray-1);
}

/**
 * Hide the footer close button by default, it should be shown in mobile.
 */
.close-button--footer {
  display: none;
}

/**
 * There are two close buttons rendered: one in the header and the other in the
 * footer. On small screens we want to show the close button in the footer but 
 * on larger screens we want to show it in the header.
 */
@media screen and (max-width: 400px) {
  .close-button--header {
    display: none;
  }

  .close-button--footer {
    display: block;
  }
}

.close-icon {
  display: block;
}

/**
 * For some reason the fonts in the SVG do not work correctly so we need to 
 * manually set the font family to be correct here..
 */
.image >>> text {
  font-family: "Barlow Semi Condensed", sans-serif;
}

.attribution {
  color: var(--color-gray-7);
  text-align: center;
  margin-top: 16px;
}
</style>
