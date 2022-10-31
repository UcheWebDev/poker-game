<template>
  <div>
    
    <div class="control">
      <div>
        <input type="checkbox" id="gatherControl" v-model="gather" />
        <label for="gatherControl">Gather</label>
      </div>
      <!-- <input type="checkbox" v-for="card in cards" v-model="card.open" /> -->

      <div class="btnContainer">
        <button @click="shuffle">Shuffle</button>
        <button @click="startGame">Start Game</button>
      </div>
    </div>

    <p class="state">{{ state }}</p>

    <ul class="cards" :class="{ gather: gather }">
      <li
        class="card"
        v-for="card in cards"
        :class="{ open: card.open }"
        :data-order="card.id"
        :key="card.label"
        @click="openCard(card)"
      >
        <div class="face back"></div>
        <div class="face front">{{ getSymbol(card.label) }}</div>
        <p style="display: absolute; z-index: 999">
          {{ card.id }}
        </p>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cards: [
        {
          id: 1,
          label: "spades",
          open: false,
        },
        {
          id: 2,
          label: "hearts",
          open: false,
        },
        {
          id: 3,
          label: "clubs",
          open: false,
        },
        {
          id: 4,
          label: "diamonds",
          open: false,
        },
      ],

      symbols: [
        { label: "spades", symbol: "♠" },
        { label: "hearts", symbol: "♥" },
        { label: "diamonds", symbol: "♦" },
        { label: "clubs", symbol: "♣" },
      ],

      state: "Press the card to start a new game!",
      gather: true,
      question: null,
      mode: "",
      count: 0,
    };
  },

  methods: {
    getSymbol(label) {
      let result = this.symbols.find((symbol) => symbol.label === label);
      return result ? result.symbol : label;
    },
    shuffle() {
      let newOrder = [1, 2, 3, 4].sort(() => Math.random() - 0.5);
      this.cards.forEach((card, index) => (card.id = newOrder[index]));
    },
    turnAllCard(state) {
      this.cards.forEach((card) => (card.open = state));
    },
    startShuffle() {
      this.shuffle();
      if (this.count++ < 6) {
        setTimeout(()=>{
          this.startShuffle()
        }, 300);
      } else {
        this.state = `Please pick out ${this.question.label} ${this.question.symbol}!!`;
        this.mode = "answer";
      }
    },
    startGame() {
      this.mode = "stratgame";
      this.question = this.symbols[parseInt(Math.random() * 3)];
      this.turnAllCard(false);
      this.gather = true;
      this.state = "Ready...";
      setTimeout(() => {
        this.gather = false;
        this.state = "Your mission is...";
      }, 1000);
      setTimeout(() => {
        this.turnAllCard(true);
        this.state = `Find ${this.question.label} ${this.question.symbol}!!`;
      }, 2000);
      setTimeout(() => {
        this.turnAllCard(false);
        this.state = `Get Ready...`;
      }, 4000);
      this.count = 0;
      setTimeout(() => {
        this.startShuffle();
      }, 6000);
    },
    getCard(label) {
      return this.cards.find((card) => card.label === label);
    },
    openCard(card) {
      if (this.mode === "") this.startGame();
      if (this.mode !== "answer") return;
      card.open = !card.open;
      if (card.label === this.question.label) {
        this.state = `🤙🤙🤙🦀  You get the ${this.question.label} ${this.question.symbol}!!!  🦀🤟🤟🤟`;
      } else {
        this.state = "👎👎👎🐧 You lose!!! 🐧 👎👎👎";
        let card = this.getCard(this.question.label);
        setTimeout(() => (card.open = true), 1000);
      }
      setTimeout(this.startGame, 3000);
    },
  },
};
</script>


<style scoped>
p.state {
  text-align: center;
  font-size: clamp(1.2rem, 2.4vw, 2.4rem);
}

.control {
  margin-top: 2rem;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 2.4rem;
}
.control .btnContainer {
  display: flex;``````````````````````````
  justify-content: center;
  align-items: center;
  gap: 1.5rem;
}
.control .btnContainer button {
  display: block;
  padding: 0.24rem 0.8rem;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1.2rem;
  background-color: #9ace98;
}
.control .btnContainer button:active {
  transform: translateY(0.24rem);
}

.cards {
  width: 100vw;
  height: 40vh;
  list-style: none;
  transition: 0.8s;
  perspective: 800px;
  margin: 0 auto;
}
.cards .card {
  width: 15vw;
  height: 21vw;
  background-color: #fff;
  position: absolute;
  top: 35%;
  left: calc(50% - 15vw / 2);
  border-radius: 5px;
  box-shadow: 0 0.8rem 2rem rgba(0, 0, 0, 0.24);
  transform-style: preserve-3d;
  transition: left 0.5s, top 0.5s, transform 0.8s;
  cursor: pointer;
}
.cards .card .face {
  background-color: #fff;
  color: #111;
  position: absolute;
  inset: 0;
  border-radius: 5px;
  backface-visibility: hidden;
}
.cards .card .face.front {
  transform: rotateY(180deg);
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 30px;
}
.cards .card .face.back::before {
  content: "";
  display: block;
  position: absolute;
  inset: 10px;
  border: 3px solid #ed4747;
  background-image: linear-gradient(-60deg, transparent 40%, #ed4747 40%, #ed4747 60%, transparent 60%), linear-gradient(60deg, transparent 40%, #ed4747 40%, #ed4747 60%, transparent 60%);
  background-size: 8px 8px;
}
.cards .card.open {
  transform: rotateY(180deg);
}
/* .cards .card:hover {
  top: 30%;
} */

.cards .card[data-order="1"] {
  left: calc(20% - 15vw / 2);
}
.cards .card[data-order="2"] {
  left: calc(40% - 15vw / 2);
}
.cards .card[data-order="3"] {
  left: calc(60% - 15vw / 2);
}
.cards .card[data-order="4"] {
  left: calc(80% - 15vw / 2);
}
.cards.gather {
  cursor: pointer;
  width: calc(15vw + 8rem);
}
.cards.gather .card {
  left: calc(50% - 15vw / 2);
  top: 20%;
  transform: rotate(360deg);
}
.cards.gather:hover .card[data-order="1"] {
  transform: rotate(355deg);
}
.cards.gather:hover .card[data-order="2"] {
  transform: rotate(360deg);
}
.cards.gather:hover .card[data-order="3"] {
  transform: rotate(365deg);
}
.cards.gather:hover .card[data-order="4"] {
  transform: rotate(370deg);
}
</style>