<template>
  <component
    :is="is"
    type="button"
    class="choice"
    :class="
      `choice--${choice ?? 'empty'} choice--${size} ${isHighlighted &&
        'choice--highlighted'}`
    "
    @click="handleClick"
  >
    <div class="content">
      <rock-icon class="icon" v-if="choice === 'rock'" />
      <scissors-icon class="icon" v-if="choice === 'scissors'" />
      <paper-icon class="icon" v-if="choice === 'paper'" />
    </div>
  </component>
</template>

<script lang="ts">
import { PropType, defineComponent } from "vue";
import { Option } from "@/types";
import RockIcon from "@/assets/icon-rock.svg?inline";
import ScissorsIcon from "@/assets/icon-scissors.svg?inline";
import PaperIcon from "@/assets/icon-paper.svg?inline";

type Props = {
  choice?: Option;
};

export default defineComponent({
  components: {
    RockIcon,
    ScissorsIcon,
    PaperIcon
  },

  props: {
    choice: {
      type: String as PropType<Props["choice"]>
    },
    is: {
      type: String as PropType<"button" | "div">,
      default: "button"
    },
    size: {
      type: String as PropType<"medium" | "large">,
      default: "medium"
    },
    isHighlighted: {
      type: Boolean,
      default: false
    }
  },

  emits: {
    click: () => {
      return true;
    }
  },

  setup(props, ctx) {
    const handleClick = () => {
      ctx.emit("click");
    };

    return {
      handleClick
    };
  }
});
</script>

<style scoped>
.choice {
  border: none;
  border-radius: var(--border-radius-circle);
  padding: 24px;
  box-shadow: inset 0 -6px 0 0 rgba(0, 0, 0, 0.2);
  width: 180px;
}

@media screen and (max-width: 400px) {
  .choice {
    width: 130px;
    padding: 16px;
  }

  .choice svg {
    transform: scale(0.8);
  }
}

.content {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: var(--color-white);
  border-radius: var(--border-radius-circle);
  width: 100%;
  height: 100%;
  box-shadow: inset 0 6px 0 0 rgba(0, 0, 0, 0.1);
}

.content:after {
  content: "";
  display: block;
  padding-bottom: 100%;
}

.choice--paper {
  background: linear-gradient(
    135deg,
    hsl(230, 89%, 65%) 0%,
    hsl(230, 89%, 62%) 100%
  );
}

.choice--scissors {
  background: linear-gradient(
    135deg,
    hsl(40, 84%, 53%) 0%,
    hsl(39, 89%, 49%) 100%
  );
}

.choice--rock {
  background: linear-gradient(
    135deg,
    hsl(349, 70%, 56%) 0%,
    hsl(349, 71%, 52%) 100%
  );
}

.choice--empty.choice--empty {
  transform: scale(0.8);
  background-color: rgba(0, 0, 0, 0.1);
  box-shadow: none;
}

.choice--empty .content.content {
  background-color: transparent;
  box-shadow: none;
}

.choice--large {
  width: 312px;
  padding: 40px;
  box-shadow: inset 0 -16px 0 0 rgba(0, 0, 0, 0.2);
}

/**
 * I couldn't figure out how to increase the size of the SVG inside the circle
 * so let's just bump up the scale.
 */
.choice--large svg {
  transform: scale(2);
}

.choice--large .content {
  box-shadow: inset 0 16px 0 0 rgba(0, 0, 0, 0.1);
}

@media screen and (max-width: 400px) {
  .choice--large {
    width: 130px;
    padding: 16px;
    box-shadow: inset 0 -6px 0 0 rgba(0, 0, 0, 0.2);
  }

  .choice--large svg {
    transform: scale(0.8);
  }

  .choice--large .content {
    box-shadow: inset 0 6px 0 0 rgba(0, 0, 0, 0.1);
  }
}

.choice--highlighted {
  box-shadow: inset 0 -6px 0 0 rgba(0, 0, 0, 0.2),
    0 0 200px 10px rgba(255, 255, 255, 0.5);
}

.choice--large.choice--highlighted {
  box-shadow: inset 0 -16px 0 0 rgba(0, 0, 0, 0.2),
    0 0 200px 10px rgba(255, 255, 255, 0.5);
}

.icon {
  position: absolute;
}
</style>
