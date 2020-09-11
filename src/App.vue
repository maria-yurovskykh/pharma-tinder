<template>
  <div class="pharm-tinder">
    <transition name="fade">
      <div v-if="step === 1" class="home" key="1">
        <div class="home__circle"></div>
        <h1 class="home__heading">
          Допоможи
          <span class="heading__pharmacist">фармацевту</span>
        </h1>
        <h2 class="home__welcome">Ласкаво просимо y гру</h2>
        <button class="home__start" @click="setStep(2)">Розпочати</button>
      </div>

      <div v-else-if="step === 2" class="game" key="2">
        <div class="game__side-bar">
          <button class="side-bar__home" @click="setStep(1)"></button>
          <button class="side-bar__retry" @click="setStep(2)"></button>
          <p class="side-bar__parameters">Параметри:</p>
          <span class="side-bar__parameter --sad">{{sad}}</span>
          <span class="side-bar__parameter --happy">{{happy}}</span>
          <span class="side-bar__parameter --heart">{{heart}}</span>
          <div class="side-bar__counter">
            <p class="counter__text">Залишилось у черзі:</p>
            <p class="counter__left">{{queueCount}}/{{clientsDataLength}}</p>
          </div>
        </div>

        <div class="game__screen">
          <vue-swing
            @throwoutleft="throwoutleft"
            @throwoutup="throwoutup"
            @throwoutright="throwoutright"
            :config="vueSwingConfig"
            ref="vueswing"
          >
            <div
              class="screen__card"
              :class="{
                'purple': throwoutleft,
                'blue': throwoutup,
                'yellow': throwoutright,
              }"
              v-for="(client, index) in clients"
              :key="index"
            >
              <div class="card__image-wrapper">
                <img class="card__image" :src="client.img" alt="Client's photo" />
                <div class="card__gradient"></div>
              </div>
              <p class="card__heading">{{client.heading}}</p>
              <p class="card__content">{{client.problem}}</p>
            </div>
          </vue-swing>
          <button class="screen__button --purple" @click="throwleft()">Препарат 1</button>
          <button class="screen__button --blue" @click="throwup()">Препарат 2</button>
          <button class="screen__button --yellow" @click="throwright()">Препарат 3</button>
        </div>
      </div>

      <!-- Rename -->
      <div v-else-if="step === 3" class="final" key="3">
        <button class="final__home" @click="setStep(1)"></button>
        <div class="final__counter">
          <div class="counter__percents --drug-1">{{countPercentOfSales(this.sad)}}%</div>
          <div class="counter__percents --drug-2">{{countPercentOfSales(this.happy)}}%</div>
          <div class="counter__percents --drug-3">{{countPercentOfSales(this.heart)}}%</div>
          <div class="counter__drug --one">Препарат 1</div>
          <div class="counter__drug --two">Препарат 2</div>
          <div class="counter__drug --three">Препарат 3</div>
        </div>
        <div class="final__result">
          <p class="result__your-result">Ваш результат:</p>
          <p class="result__result">«Що я тут роблю?»</p>
          <p
            class="result__content"
          >Це тестове завдання, так що не будемо заглиблюватися в глибини проблем фармацевтів.</p>
        </div>
        <button class="final__restart" @click="setStep(2)">Спробувати ще</button>
      </div>
    </transition>
  </div>
</template>

<script>
import VueSwing from "vue-swing";
import { clientsData } from "./clientsData.js";

export default {
  name: "App",

  components: {
    VueSwing,
  },

  settings: {
    clientsData,
  },

  computed: {
    clientsDataLength() {
      return this.$options.settings.clientsData.length;
    },
  },

  data() {
    return {
      step: 1,
      sad: 0,
      happy: 0,
      heart: 0,
      currentClient: 0,
      queueCount: this.clientsDataLength,
      clients: [...this.$options.settings.clientsData],

      vueSwingConfig: {
        allowedDirections: [
          VueSwing.Direction.UP,
          VueSwing.Direction.LEFT,
          VueSwing.Direction.RIGHT,
        ],
      },
    };
  },

  methods: {
    methodName() {
      return "sad";
    },

    reset() {
      this.sad = 0;
      this.happy = 0;
      this.heart = 0;
      this.currentClient = 0;
      this.queueCount = this.clientsDataLength;
      this.clients = [...this.$options.settings.clientsData];
    },

    setStep(stepNumer) {
      this.step = stepNumer;
      if (stepNumer === 2) {
        this.reset();
      }
    },

    incrementSale(drugType) {
      switch (drugType) {
        case "sad":
          this.sad++;
          break;
        case "happy":
          this.happy++;
          break;
        case "heart":
          this.heart++;
          break;
      }
      this.updateQueue();
      setTimeout(() => {
        this.clients.pop();
      }, 500);
    },

    updateQueue() {
      this.currentClient++;
      this.queueCount--;
      if (this.currentClient === this.clientsDataLength) {
        setTimeout(() => {
          this.setStep(3);
        }, 500);
      }
    },

    countPercentOfSales(drugType) {
      return Math.round((drugType * 100) / this.clientsDataLength);
    },

    throwoutleft() {
      this.incrementSale("sad");
    },

    throwoutup() {
      this.incrementSale("happy");
    },

    throwoutright() {
      this.incrementSale("heart");
    },

    throwleft() {
      const cards = this.$refs.vueswing.cards;
      cards[cards.length - 1].throwOut(-50, 0);
    },

    throwup() {
      const cards = this.$refs.vueswing.cards;
      cards[cards.length - 1].throwOut(0, -50);
    },

    throwright() {
      const cards = this.$refs.vueswing.cards;
      cards[cards.length - 1].throwOut(50, 0);
    },
  },
};
</script>
