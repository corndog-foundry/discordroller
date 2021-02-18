<template>
  <div>
    <div class="top-vars">
      <div class="top-var"></div>

      <div class="top-var">
        <h5>Number of Dice</h5>
        <input type="number" v-model="numDice" min="1" max="99"/>
      </div>

      <div class="top-var">
        <h5>Bonus</h5>
        <input type="number" v-model="bonus" min="-99" max="99"/>
      </div>

      <div class="top-var"></div>
    </div>

    <br>

    <div class="buttons">
      <button @click="rollDice(4)">d4</button>
      <button @click="rollDice(6)">d6</button>
      <button @click="rollDice(8)">d8</button>
      <button @click="rollDice(10)">d10</button>
      <button @click="rollDice(12)">d12</button>
      <button @click="rollDice(20)">d20</button>
      <button @click="rollDice(100)">d100</button>
      <button @click="changeWebHook()">&lt;- Submit a new Discord Webhook URL</button>
    </div>
  </div>
</template>

<script>
  export default {
    name: "Roller",
    data () {
      return {
        numDice: 1,
        bonus: 0
      }
    },
    methods: {
      rollDice: function (dieType) {
        this.$emit('event:Roll', this.numDice, dieType, this.bonus);
      },
      changeWebHook: function () {
        this.$emit('revert:DiscordKey');
      }
    }
  }
</script>

<style scoped>
  .top-vars {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  }

  .buttons {
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  button {
    width: 30vw;
    margin-bottom: 10px;
  }

  * {
    justify-content: center;
    align-items: center;
    align-content: center;
  }
</style>
