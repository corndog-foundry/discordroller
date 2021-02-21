<template>
  <div>
    <div class="roll-text" @click="roll">
      <p>{{ rollFormula }}</p>
    </div>

    <div class="die-row">
      <div/>
      <div>
        <button class="normal" @click="bumpBlack"/>
        <p v-if="rollLabels">Normal</p>
      </div>
      <div/>
    </div>

    <div class="die-row">
      <div/>
      <div>
        <button class="hunger" @click="bumpRed"/>
        <p v-if="rollLabels">Hunger</p>
      </div>
      <div/>
    </div>
  </div>
</template>

<script>
  export default {
    name: "VampireRoller",
    props: ['rollLabels'],
    data () {
      return {
        nRed: 0,
        nBlack: 0,
        rollFormula: ''
      }
    },
    methods: {
      roll: function () {
        this.$emit('event:Roll', this.nBlack, this.nRed, this.rollFormula);
        this.reset();
      },

      calcRollFormula: function () {
        let formula = '';

        if (this.nBlack > 0) formula += `${this.nBlack} normal`
        if (this.nRed > 0) {
          if (formula === '') {
            formula += `${this.nRed} hunger`
          } else {
            formula += ` + ${this.nRed} hunger`
          }
        }

        this.rollFormula = formula;
      },
      bumpRed: function () {
        this.nRed++;
        this.calcRollFormula();
      },
      bumpBlack: function () {
        this.nBlack++;
        this.calcRollFormula();
      },
      reset: function () {
        this.nBlack = 0;
        this.nRed = 0;
        this.rollFormula = '';
      }
    }
  }
</script>

<style scoped>
  .normal {
    background-image: url("../img/vampire-black.png");
  }

  .hunger {
    background-image: url("../img/vampire-red.png");
  }

  .normal:hover,
  .hunger:hover {
    background-image: url("../img/vampire-selected.png");
  }

  button {
    height: 128px;
    width: 128px;
    border: none;
  }

  .die-row {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin-bottom: 20px;
  }

  .roll-text {
    background-color: #0c1e30;
    border: 1px solid #0c0e10;
    margin-bottom: 20px;
    height: 50px;
  }

  .roll-text:hover {
    background-color: #FFFFFF;
    color: #000000;
  }

  p {
    background-color: inherit;
    color: inherit;
  }
</style>
