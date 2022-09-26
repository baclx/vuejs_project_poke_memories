<template>
  <main-screen 
    v-if="statusMatch === 'default'" 
    @onStart="onHandleBeforeStart($event)" 
  />
  <interact-screen 
    v-if="statusMatch === 'match'" 
    :cardsContext = "settings.cardsContext"
    @onFinish="onGetResult()"
  />
  <result-screen
    v-if="statusMatch === 'result'"
    :timer="timer"
    @onStartAgain="statusMatch = 'default'"
  />
  <copy-right />
</template>

<script>
import MainScreen from './components/MainScreen.vue'
import InteractScreen from './components/Interact.vue'
import ResultScreen from './components/ResultScreen.vue'
import CopyRight from './components/CopyRight.vue'

import { shuffled } from './utils/array'

export default {
  name: 'App',
  data() {
    return {
      settings: {
        totalOfBlocks: 0,
        cardsContext: [],
        startedAt: null,
      },
      statusMatch: "default",
      timer: 0,
    }
  },
  components: {
    MainScreen,
    InteractScreen,
    ResultScreen,
    CopyRight,
  },
  methods: {
    onHandleBeforeStart(config) {
      // console.log(config);
      this.settings.totalOfBlocks = config.totalOfBlocks;

      const firstCards = Array.from(
        {length: this.settings.totalOfBlocks / 2},
        // moi cai index cach nhau 1 don vi
        // k co cai duoi thì index cua mang la undefined
        (_, i) => i + 1
      );

      const secondCards = [...firstCards];

      const cards = [...firstCards, ...secondCards];

      this.settings.cardsContext = shuffled(shuffled(cards));
      // console.log(this.settings.cardsContext);
      this.settings.startedAt = new Date().getTime();

      this.statusMatch = 'match';
    },
    onGetResult() {
      // get timer
      this.timer = new Date().getTime - this.settings.startedAt;

      // switch to result component
      this.statusMatch = "result";
    }
  },
}
</script>