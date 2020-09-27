<template>
  <div class="background-container">
    <div class="container">
      <div class="header">
        <logo-icon class="logo" alt="Rock Paper Scissors logo" />
        <div class="score-container">
          <div class="score-title">Score</div>
          <div class="score-number">{{ score }}</div>
        </div>
      </div>

      <div
        v-if="!choice"
        class="selections-container"
        :class="{ 'selections-container--has-winner': winner }"
      >
        <div class="selections-top-container">
          <choice choice="paper" @click="handleSelected('paper')" />
          <choice choice="scissors" @click="handleSelected('scissors')" />
        </div>

        <div class="selections-bottom-container">
          <choice choice="rock" @click="handleSelected('rock')" />
        </div>
      </div>

      <div v-else-if="choice" class="chosen-container">
        <div class="chosen-selection">
          <div class="chosen-selection__text">You picked</div>
          <choice
            is="div"
            size="large"
            :choice="choice"
            :isHighlighted="winner === 'player'"
          />
        </div>
        <div
          class="result-container"
          :class="!winner && 'result-container--hidden'"
        >
          <div v-if="winner === 'player'" class="winner-message">You win</div>
          <div v-else-if="winner === 'computer'" class="winner-message">
            You lose
            {{ winner === "computer" }}
          </div>
          <div v-else-if="winner === 'draw'" class="winner-message">Draw</div>
          <div v-else class="winner-message">No result</div>
          <button
            type="button"
            @click="restart"
            class="button play-again-button"
            :class="winner === 'computer' && 'play-again-button--red'"
          >
            Play again
          </button>
        </div>
        <div class="chosen-selection">
          <div class="chosen-selection__text">The house picked</div>
          <choice
            is="div"
            size="large"
            :isHighlighted="winner === 'computer'"
            :choice="computerChoice"
          />
        </div>
      </div>

      <div class="rules-footer-container">
        <button
          type="button"
          @click="toggleRulesOpen"
          class="button rules-button"
        >
          Rules
        </button>
      </div>
    </div>

    <rules-modal :isOpen="isRulesOpen" @close="toggleRulesOpen" />
  </div>
</template>

<script lang="ts">
import { ref, watchEffect, defineComponent } from "vue";
import { Option } from "@/types";
import Choice from "./Choice.vue";
import LogoIcon from "../assets/logo.svg?inline";
import RulesModal from "./RulesModal.vue";

/**
 * Map of which option beats which option. Assume that each option can only win
 * agains one other option. If the matchup is not described in this map, then
 * it is a draw
 */
const BEATS: Record<Option, Option> = {
  rock: "scissors",
  scissors: "paper",
  paper: "rock"
};

/**
 * Generate a choice for the computer with an amazingly complex algorithm.
 */
const getComputerChoice = (): Option => {
  const options: Option[] = ["rock", "paper", "scissors"];
  return options[Math.floor(Math.random() * options.length)];
};

/**
 * The main component for the rock-paper-scissors game.
 *
 * The main state of the game is determined with 3 variables: `choice`,
 * `computerChoice`, and `winner`. The flow of the game goes like so:
 *
 * 1. The player hasn't made a choice (`choice` = `undefined`)
 * 2. The player makes a choice (`choice` = 'something', `computerChoice` =
 *    `undefined`)
 * 3. A choice is generated for the computer (`computerChoice` = 'something')
 * 4. A winner is determined (`winner` = 'player')
 * 5. The game is reset (`choice`, `computerChoice`, `winner` = `undefined`)
 */
export default defineComponent({
  components: {
    Choice,
    LogoIcon,
    RulesModal
  },

  setup() {
    const isRulesOpen = ref(false);
    const toggleRulesOpen = () => {
      isRulesOpen.value = !isRulesOpen.value;
    };

    const score = ref(0);

    const choice = ref<Option | undefined>(undefined);
    const computerChoice = ref<Option | undefined>(undefined);

    const generateComputerChoice = () => {
      computerChoice.value = getComputerChoice();
    };
    const handleSelected = (option: Option) => {
      choice.value = option;
    };

    const winner = ref<"player" | "computer" | "draw" | undefined>(undefined);
    /**
     * Check who the winner is from the current state.
     */
    const checkWinner = () => {
      if (!choice.value || !computerChoice.value) {
        return;
      }

      if (BEATS[choice.value] === computerChoice.value) {
        winner.value = "player";
        score.value++;
      } else if (BEATS[computerChoice.value] === choice.value) {
        winner.value = "computer";
        score.value--;
      } else {
        winner.value = "draw";
      }
    };

    /**
     * When the player has made a choice, generate a choice for the computer
     * after a short delay.
     */
    watchEffect(onInvalidate => {
      if (choice.value) {
        const timeoutID = setTimeout(() => {
          generateComputerChoice();
        }, 1000);

        onInvalidate(() => {
          clearTimeout(timeoutID);
        });
      }
    });

    /**
     * When both the computer and the player has made a choice, declare the
     * winner after a short delay.
     */
    watchEffect(onInvalidate => {
      if (computerChoice.value) {
        const timeoutID = setTimeout(() => {
          checkWinner();
        }, 1000);

        onInvalidate(() => {
          clearTimeout(timeoutID);
        });
      }
    });

    /**
     * Restart the game by setting all of the variables to their initial state.
     */
    const restart = () => {
      winner.value = undefined;
      choice.value = undefined;
      computerChoice.value = undefined;
    };

    return {
      score,
      choice,
      computerChoice,
      winner,
      isRulesOpen,
      toggleRulesOpen,
      handleSelected,
      restart
    };
  }
});
</script>

<style scoped>
.button {
  background-color: var(--color-white);
  border: none;
  border-radius: var(--border-radius-md);
  padding: 16px 24px;
  font-size: var(--font-size);
  transition: background-color 150ms ease-in-out;
  font-family: "Barlow Semi Condensed", sans-serif;
  font-weight: var(--font-weight);
  letter-spacing: var(--letter-spacing-loose);
  text-transform: uppercase;
}

@media screen and (max-width: 400px) {
  .button {
    font-size: var(font-size-sm);
  }
}

.background-container {
  min-height: 100vh;
  background: radial-gradient(at top, hsl(214, 47%, 23%), hsl(237, 49%, 15%));
  color: var(--color-white);
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  padding: 32px 16px;
  min-height: 100vh;
}

@media screen and (max-width: 400px) {
  .container {
    padding: 24px;
  }
}

.header {
  display: flex;
  justify-content: space-between;
  width: var(--container-width);
  max-width: 100%;
  margin: 0 auto;
  margin-bottom: 48px;
  padding: 16px;
  border: 4px solid var(--color-gray-3);
  border-radius: var(--border-radius-xl);
}

@media screen and (max-width: 400px) {
  .header {
    align-self: stretch;
    width: initial;
    margin-left: 0;
    margin-right: 0;
    padding: 12px;
  }
}

.logo {
  margin: 8px;
  width: 150px;
}

@media screen and (max-width: 400px) {
  .logo {
    width: 100px;
  }
}

.score-container {
  padding: 16px 48px;
  border-radius: var(--border-radius-md);
  background-color: var(--color-white);
  text-align: center;
}

@media screen and (max-width: 400px) {
  .score-container {
    padding: 16px 24px;
  }
}

.score-title {
  letter-spacing: var(--letter-spacing-loose);
  color: var(--color-blue-5);
  text-transform: uppercase;
}

@media screen and (max-width: 400px) {
  .score-title {
    font-size: var(--font-size-sm);
    letter-spacing: var(--letter-spacing);
  }
}

.score-number {
  font-size: var(--font-size-xxxl);
  font-weight: var(--font-weight-bold);
  line-height: 1;
  color: var(--color-gray-7);
}

@media screen and (max-width: 400px) {
  .score-number {
    font-size: var(--font-size-xl);
  }
}

.selections-container {
  width: 440px;
  max-width: 100%;
  margin: 0 auto;
  background-image: url("../assets/bg-triangle.svg");
  background-repeat: no-repeat;
  background-position: center 70%;
}

@media screen and (max-width: 400px) {
  .selections-container {
    width: initial;
    align-self: stretch;
    margin-left: 0;
    margin-right: 0;
    background-size: 60%;
    background-position: center 60%;
  }
}

.selections-container--has-winner {
  max-width: 1200px;
}

.selections-top-container {
  display: flex;
  justify-content: space-between;
  margin-bottom: 24px;
}

.selections-bottom-container {
  display: flex;
  justify-content: center;
}

.chosen-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

@media screen and (max-width: 400px) {
  .chosen-container {
    flex-flow: wrap;
    align-items: flex-start;
  }
}

.chosen-selection {
  display: flex;
  flex-direction: column;
  text-align: center;
  padding: 40px;
}

@media screen and (max-width: 400px) {
  .chosen-selection {
    flex-direction: column-reverse;
    padding: 16px;
    width: 50%;
  }

  .chosen-selection:first-child {
    padding-left: 0;
  }

  .chosen-selection:last-child {
    padding-right: 0;
  }

  .chosen-selection .choice {
    width: 100%;
  }
}

.chosen-selection__text {
  margin-bottom: 32px;
  font-size: var(--font-size-md);
  letter-spacing: var(--letter-spacing-loose);
  text-transform: uppercase;
}

@media screen and (max-width: 400px) {
  .chosen-selection__text {
    margin-bottom: 16px;
    margin-top: 24px;
    font-size: var(--font-size);
  }
}

.result-container {
  padding-left: 32px;
  padding-right: 32px;
}

@media screen and (max-width: 400px) {
  /**
   * On small screens the results should be shown *under* the choices.
   */
  .result-container {
    order: 3;
  }
}

.result-container--hidden {
  display: none;
}

@media screen and (max-width: 400px) {
  .result-container--hidden {
    display: block;
    visibility: hidden;
  }
}

.winner-message {
  font-size: var(--font-size-xxl);
  font-weight: var(--font-weight-bold);
  text-transform: uppercase;
  margin-bottom: 16px;
  padding-left: 16px;
  padding-right: 16px;
  text-align: center;
}

@media screen and (max-width: 400px) {
  .winner-message {
    font-size: var(font-size-xl);
  }
}

.play-again-button {
  width: 100%;
  background-color: var(--color-white);
  text-transform: uppercase;
}

.play-again-button:hover {
  background-color: var(--color-gray-1);
}

.play-again-button--red {
  color: hsl(349, 71%, 52%);
}

.rules-footer-container {
  display: flex;
  justify-content: flex-end;
  margin: 0 auto;
  width: var(--container-width);
  max-width: 100%;
}

@media screen and (max-width: 400px) {
  .rules-footer-container {
    flex-direction: column;
    justify-content: center;
  }
}

.rules-button {
  padding-left: 40px;
  padding-right: 40px;
  border: 2px solid var(--color-gray-3);
  background-color: transparent;
  color: var(--color-white);
}

.rules-button:hover {
  background-color: var(--color-gray-7);
}
</style>
