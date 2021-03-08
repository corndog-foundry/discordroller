<template>
  <div>
    <AddBonus
        v-if="gettingBonus"
        @set:Bonus="setBonus"
        @cancel:SetBonus="cancelBonus"
    />
    <div v-else>
      <div class="roll-text" @click="roll">
        <p>{{ rollFormula }}</p>
      </div>

      <div class="die-row">
        <div/>
        <div>
          <button class="d4" @click="bumpd4"/>
          <p v-if="rollLabels">d4</p>
        </div>

        <div>
          <button class="d6" @click="bumpd6"/>
          <p v-if="rollLabels">d6</p>
        </div>

        <div>
          <button class="d8" @click="bumpd8"/>
          <p v-if="rollLabels">d8</p>
        </div>
        <div/>
      </div>

      <div class="die-row">
        <div/>
        <div>
          <button class="d10" @click="bumpd10"/>
          <p v-if="rollLabels">d10</p>
        </div>

        <div>
          <button class="d12" @click="bumpd12"/>
          <p v-if="rollLabels">d12</p>
        </div>

        <div>
          <button class="d20" @click="bumpd20"/>
          <p v-if="rollLabels">d20</p>
        </div>
        <div/>
      </div>

      <div class="bonus-section">
        <h3>Bonus: </h3>
        <label>
          <input class="bonus-input" type="number" v-model="bonus" min="-99" max="99" @change="calcRollFormula"/>
        </label>
      </div>
    </div>
  </div>
</template>

<script>
  import AddBonus from "@/components/AddBonus";
  export default {
    name: "StandardRoller",
    props: ['rollLabels'],
    components: {AddBonus},
    data () {
      return {
        rollFormula: '',
        d4: 0,
        d6: 0,
        d8: 0,
        d10: 0,
        d12: 0,
        d20: 0,
        bonus: 0,
        gettingBonus: false,
      }
    },
    methods: {
      calcRollFormula: function () {
        let formula = '';

        if (this.d4 > 0) formula += `${this.d4}d4`
        if (this.d6 > 0) {
          if (formula === '') {
            formula += `${this.d6}d6`
          } else {
            formula += ` + ${this.d6}d6`
          }
        }
        if (this.d8 > 0) {
          if (formula === '') {
            formula += `${this.d8}d8`
          } else {
            formula += ` + ${this.d8}d8`
          }
        }
        if (this.d10 > 0) {
          if (formula === '') {
            formula += `${this.d10}d10`
          } else {
            formula += ` + ${this.d10}d10`
          }
        }
        if (this.d12 > 0) {
          if (formula === '') {
            formula += `${this.d12}d12`
          } else {
            formula += ` + ${this.d12}d12`
          }
        }
        if (this.d20 > 0) {
          if (formula === '') {
            formula += `${this.d20}d20`
          } else {
            formula += ` + ${this.d20}d20`
          }
        }
        if (this.bonus > 0) {
          if (formula === '') {
            formula += `${this.bonus}`
          } else {
            formula += ` + ${this.bonus}`
          }
        } else if (this.bonus < 0) {
          if (formula === '') {
            formula += `${this.bonus}`
          } else {
            formula += ` - ${Math.abs(this.bonus)}`
          }
        }

        this.rollFormula = formula;
      },
      bumpd4: function () {
        this.d4++;
        this.calcRollFormula();
      },
      bumpd6: function () {
        this.d6++;
        this.calcRollFormula();
      },
      bumpd8: function () {
        this.d8++;
        this.calcRollFormula();
      },
      bumpd10: function () {
        this.d10++;
        this.calcRollFormula();
      },
      bumpd12: function () {
        this.d12++;
        this.calcRollFormula();
      },
      bumpd20: function () {
        this.d20++;
        this.calcRollFormula();
      },
      roll: function () {
        this.$emit('event:Roll', this.d4, this.d6, this.d8, this.d10, this.d12, this.d20, this.bonus, this.rollFormula);
        this.reset();
      },
      reset: function () {
        this.rollFormula = ''
        this.d4 = 0
        this.d6 = 0
        this.d8 = 0
        this.d10 = 0
        this.d12 = 0
        this.d20 = 0
        this.bonus = 0
      },
      setBonus: function (newBonus) {
        this.bonus = newBonus;
        this.gettingBonus = false;
        this.calcRollFormula();
      },
      cancelBonus: function () {
        this.gettingBonus = false;
      },
      getBonus: function () {
        this.gettingBonus = true;
      }
    }
  }
</script>

<style scoped>
  .die-row {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin-bottom: 20px;
  }

  button {
    height: 128px;
    width: 128px;
    border: none;
  }

  .d4 {
    background-image: url("../img/standard-d4.png");
  }

  .d4:hover {
    background-image: url("../img/standard-d4-selected.png");
  }

  .d6 {
    background-image: url("../img/standard-d6.png");
  }

  .d6:hover {
    background-image: url("../img/standard-d6-selected.png");
  }

  .d8 {
    background-image: url("../img/standard-d8.png");
  }

  .d8:hover {
    background-image: url("../img/standard-d8-selected.png");
  }

  .d10 {
    background-image: url("../img/standard-d10.png");
  }

  .d10:hover {
    background-image: url("../img/standard-d10-selected.png");
  }

  .d12 {
    background-image: url("../img/standard-d12.png");
  }

  .d12:hover {
    background-image: url("../img/standard-d12-selected.png");
  }

  .d20 {
    background-image: url("../img/standard-d20.png");
  }

  .d20:hover {
    background-image: url("../img/standard-d20-selected.png");
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

  .bonus-section {
    display: flex;
    flex-direction: row
  }

  .bonus-input {
    margin-left: 20px;
    margin-top: 19px;
  }
</style>
