<template>
  <div id="app">
    <div class="icon" v-if="!keyInput"/>
    <h1 class="title" @click="revertDiscordKey">Dicecord</h1>
    <VersionText
        v-if="!keyInput"
        :version="version"
    />

    <div v-if="keyInput">
      <div class="top-selects">
        <label>
          Roll Type:
          <select v-model="selectedType">
            <option v-for="type in diceTypes" :key="type">{{ type }}</option>
          </select>
        </label>
        <label>
          Roll Context:
          <input type="text" v-model="rollContext"/>
        </label>
      </div>

      <hr>

      <StandardRoller
          v-if="selectedType === 'Standard'"
          @event:Roll="standardRoll"
          :rollLabels = "rollLabels"
      />

      <StarWarsRoller
        v-if="selectedType === 'Star Wars / Genesys'"
        @event:StandardRoll="starWarsRoll"
        @event:ForceRoll="starWarsForceRoll"
        :rollLabels = "rollLabels"
      />

      <VampireRoller
        v-if="selectedType === 'Vampire the Masquerade 5e'"
        @event:Roll="vampireRoll"
        :rollLabels = "rollLabels"
      />

      <ShiverRoller
        v-if="selectedType === 'SHIVER'"
        @event:Roll="shiverRoll"
        :rollLabels = "rollLabels"
      />
    </div>
    <GetKey
        v-else
        @change:DiscordKey="receiveDiscordKey"
    />
  </div>
</template>

<script>
  import StarWarsRoller from "@/components/StarWarsRoller";
  import VampireRoller from "@/components/VampireRoller";
  import VersionText from "@/components/VersionText";
  import { version } from '../package.json';
  import GetKey from "@/components/GetKey";
  import StandardRoller from "@/components/StandardRoller";
  import ShiverRoller from "@/components/ShiverRoller";

  export default {
    name: 'App',
    components: {
      ShiverRoller,
      StandardRoller,
      GetKey,
      VersionText,
      VampireRoller,
      StarWarsRoller,
    },
    data () {
      return {
        discordKey: '',
        keyInput: false,
        userNick: '',
        diceTypes: [
            'Standard',
            'Star Wars / Genesys',
            'Vampire the Masquerade 5e',
            'SHIVER'
        ],
        selectedType: 'Standard',
        verboseMode: false,
        rollContext: '',
        version: version,
        rollLabels: false
      }
    },
    methods: {
      receiveDiscordKey: function (newKey, newNick, newVerboseMode, newRollLabels) {
        this.discordKey = newKey;
        this.userNick = newNick;
        this.verboseMode = newVerboseMode;
        this.rollLabels = newRollLabels;
        this.keyInput = true;
      },
      standardRoll: function (nD4, nD6, nD8, nD10, nD12, nD20, bonus, rollFormula) {
        let total = parseInt(bonus);
        let fields = [];

        if (nD4 > 0) {
          let d4Out = [];

          for (let i = 0; i < nD4; i++) {
            let res = this.generate(4);
            d4Out.push(res);
            total += res;
          }

          fields.push({ name: 'D4 Rolls', value: d4Out.toString()})
        }

        if (nD6 > 0) {
          let d6Out = [];

          for (let i = 0; i < nD6; i++) {
            let res = this.generate(6);
            d6Out.push(res);
            total += res;
          }

          fields.push({ name: 'D6 Rolls', value: d6Out.toString()})
        }

        if (nD8 > 0) {
          let d8Out = [];

          for (let i = 0; i < nD8; i++) {
            let res = this.generate(8);
            d8Out.push(res);
            total += res;
          }

          fields.push({ name: 'D8 Rolls', value: d8Out.toString()})
        }

        if (nD10 > 0) {
          let d10Out = [];

          for (let i = 0; i < nD10; i++) {
            let res = this.generate(10);
            d10Out.push(res);
            total += res;
          }

          fields.push({ name: 'D10 Rolls', value: d10Out.toString()})
        }

        if (nD12 > 0) {
          let d12Out = [];

          for (let i = 0; i < nD12; i++) {
            let res = this.generate(12);
            d12Out.push(res);
            total += res;
          }

          fields.push({ name: 'D12 Rolls', value: d12Out.toString()})
        }

        if (nD20 > 0) {
          let d20Out = [];

          for (let i = 0; i < nD20; i++) {
            let res = this.generate(20);
            d20Out.push(res);
            total += res;
          }

          fields.push({ name: 'D20 Rolls', value: d20Out.toString()})
        }

        if (!this.verboseMode) fields = [];

        let formula = `${rollFormula} = ${total}`;

        this.buildAndPushPayload(formula, fields);
      },
      vampireRoll: function (numBlackDice, numRedDice, rollFormula) {
        let blackRolls = [];
        let redRolls = [];

        let blackRollsOut = [];
        let redRollsOut = [];

        let successes = 0;
        let criticals = 0;

        let messyCritical = false;
        let bestialFailure = false;

        for (let i = 0; i < numBlackDice; i++) {
          blackRolls.push(this.generate(10));
        }

        blackRolls.forEach(roll => {
          if (roll > 5 && roll < 10) {
            successes++;
            blackRollsOut.push('S')
          } else if (roll === 10) {
            criticals++;
            if (criticals % 2 === 0) {
              criticals -= 2;
              successes += 4;
            }
            blackRollsOut.push('C')
          } else {
            blackRollsOut.push('F')
          }
        })

        for (let i = 0; i < numRedDice; i++) {
          redRolls.push(this.generate(10));
        }

        redRolls.forEach(roll => {
          if (roll === 1) {
            bestialFailure = true;
            redRollsOut.push('B')
          }
          else if (roll > 5 && roll < 10) {
            successes++;
            redRollsOut.push('S')
          }
          else if (roll === 10) {
            criticals++;
            if (criticals % 2 === 0) {
              criticals -= 2;
              successes += 4;
              messyCritical = true;
            }
            redRollsOut.push('M')
          } else {
            redRollsOut.push('F')
          }
        })

        successes += criticals;

        let formula = `${rollFormula} = ${successes} successes.`;
        formula = formula.replace('normal', ':black_circle:')
        formula = formula.replace('hunger', ':red_circle:')
        const fields = [];

        if (this.verboseMode) {
          if (numBlackDice > 0) fields.push({name: "Black Rolls", value: blackRollsOut.toString()})
          if (numRedDice > 0) fields.push({name: "Red Rolls", value: redRollsOut.toString()})
        }

        if (messyCritical) fields.push({name: "Messy Critical?", value: "Yes"})
        if (bestialFailure) fields.push({name: "Bestial Failure?", value: "Yes"})

        this.buildAndPushPayload(formula, fields);
      },
      generate: function (max) {
        return 1 + Math.floor(Math.random() * max);
      },
      revertDiscordKey: function () {
        this.keyInput = false;
      },
      pushPayload: function (payload) {
        fetch(this.discordKey, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(payload)
        })
      },
      expandRollFormula: function (rollFormula, expansion) {
        if (rollFormula !== '') {
          rollFormula += ` + ${expansion}`;
          return rollFormula;
        } else {
          return expansion;
        }
      },
      expandResultFormula: function (rollFormula, expansion) {
        if (rollFormula !== '') {
          rollFormula += ` | ${expansion}`;
          return rollFormula;
        } else {
          return expansion;
        }
      },
      starWarsRoll: function (nBlue, nGreen, nYellow, nBlack, nPurple, nRed, rollFormula) {
        let successes = 0;
        let advantages = 0;
        let triumph = false;
        let despair = false;

        let blueRollsOut = [];
        let greenRollsOut = [];
        let yellowRollsOut = [];
        let blackRollsOut = [];
        let purpleRollsOut = [];
        let redRollsOut = [];

        let fields = [];

        if (nBlue > 0) {
          for (let i = 0; i < nBlue; i++) {
            switch(this.generate(6)) {
              case 1:
              case 2:
                blueRollsOut.push('B');
                break;
              case 3:
                blueRollsOut.push('S');
                successes++;
                break;
              case 4:
                blueRollsOut.push('SA');
                successes++;
                advantages++;
                break;
              case 5:
                blueRollsOut.push('AA');
                advantages += 2;
                break;
              case 6:
                blueRollsOut.push('A');
                advantages++;
                break;
            }
          }

          if (this.verboseMode) fields.push({name: "Boost Rolls", value: blueRollsOut.toString()})
        }

        if (nBlack > 0) {
          for (let i = 0; i < nBlack; i++) {
            switch(this.generate(8)) {
              case 1:
              case 2:
                blackRollsOut.push('B');
                break;
              case 3:
              case 4:
                blackRollsOut.push('F');
                successes--;
                break;
              case 5:
              case 6:
                blackRollsOut.push('T');
                advantages--;
                break;
            }
          }

          if (this.verboseMode) fields.push({name: "Setback Rolls", value: blackRollsOut.toString()})
        }

        if (nGreen > 0) {
          for (let i = 0; i < nGreen; i++) {
            switch(this.generate(8)) {
              case 1:
                greenRollsOut.push('B');
                break;
              case 2:
              case 3:
                greenRollsOut.push('S');
                successes++;
                break;
              case 4:
                greenRollsOut.push('SS');
                successes += 2;
                break;
              case 5:
              case 6:
                greenRollsOut.push('A');
                advantages++;
                break;
              case 7:
                greenRollsOut.push('SA');
                successes++;
                advantages++;
                break;
              case 8:
                greenRollsOut.push('AA');
                advantages += 2;
                break;
            }
          }

          if (this.verboseMode) fields.push({name: "Ability Rolls", value: greenRollsOut.toString()})
        }

        if (nPurple > 0) {
          for (let i = 0; i < nPurple; i++) {
            switch (this.generate(8)) {
              case 1:
                purpleRollsOut.push('B');
                break;
              case 2:
                purpleRollsOut.push('F');
                successes--;
                break;
              case 3:
                purpleRollsOut.push('FF');
                successes -= 2;
                break;
              case 4:
              case 5:
              case 6:
                purpleRollsOut.push('T');
                advantages--;
                break;
              case 7:
                purpleRollsOut.push('TT');
                advantages -= 2;
                break;
              case 8:
                purpleRollsOut.push('FT');
                successes--;
                advantages--;
                break;
            }
          }

          if (this.verboseMode) fields.push({name: "Difficulty Rolls", value: purpleRollsOut.toString()})
        }

        if (nYellow > 0) {
          for (let i = 0; i < nYellow; i++) {
            switch (this.generate(12)) {
              case 1:
                yellowRollsOut.push('B');
                break;
              case 2:
              case 3:
                yellowRollsOut.push('S');
                successes++;
                break;
              case 4:
              case 5:
                yellowRollsOut.push('SS');
                successes += 2;
                break;
              case 6:
                yellowRollsOut.push('A');
                advantages++;
                break;
              case 7:
              case 8:
              case 9:
                yellowRollsOut.push('SA');
                successes++;
                advantages++;
                break;
              case 10:
              case 11:
                yellowRollsOut.push('AA');
                advantages += 2;
                break;
              case 12:
                yellowRollsOut.push('Tr');
                triumph = true;
                break;
            }
          }

          if (this.verboseMode) fields.push({name: "Proficiency Rolls", value: yellowRollsOut.toString()})
        }

        if (nRed > 0) {
          for (let i = 0; i < nRed; i++) {
            switch (this.generate(12)) {
              case 1:
                redRollsOut.push('B');
                break;
              case 2:
              case 3:
                redRollsOut.push('F');
                successes--;
                break;
              case 4:
              case 5:
                redRollsOut.push('FF');
                successes -= 2;
                break;
              case 6:
              case 7:
                redRollsOut.push('T');
                advantages--;
                break;
              case 8:
              case 9:
                redRollsOut.push('FT');
                successes--;
                advantages--;
                break;
              case 10:
              case 11:
                redRollsOut.push('TT');
                advantages -= 2;
                break;
              case 12:
                redRollsOut.push('D');
                despair = false;
                break;
            }
          }

          if (this.verboseMode) fields.push({name: "Challenge Rolls", value: redRollsOut.toString()})
        }

        if (triumph) fields.push({name: "Triumph?", value: "Yes"});
        if (despair) fields.push({name: "Despair?", value: "Yes"});

        let sResult = successes > 0 ? `Success (${successes})` : `Failure (${Math.abs(successes)})`;
        let aResult = advantages > 0 ? `Advantage (${advantages})` : `Threat (${Math.abs(advantages)})`;

        let formula = `${rollFormula} = ${sResult} + ${aResult}`;
        formula = formula.replace('boost', ':blue_circle:');
        formula = formula.replace('setback', ':black_circle:');
        formula = formula.replace('ability', ':green_circle:');
        formula = formula.replace('difficulty', ':purple_circle:');
        formula = formula.replace('proficiency', ':yellow_circle:');
        formula = formula.replace('challenge', ':red_circle:');

        this.buildAndPushPayload(formula, fields);
      },
      starWarsForceRoll: function (nWhite) {
        let darkSidePoints = 0;
        let lightSidePoints = 0;

        let whiteRollsOut = [];

        for (let i = 0; i < nWhite; i++) {
          switch (this.generate(12)) {
            case 1:
            case 2:
            case 3:
            case 4:
            case 5:
            case 6:
              whiteRollsOut.push('D');
              darkSidePoints++;
              break;
            case 7:
              whiteRollsOut.push('DD');
              darkSidePoints += 2;
              break;
            case 8:
            case 9:
              whiteRollsOut.push('L');
              lightSidePoints++;
              break;
            case 10:
            case 11:
            case 12:
              whiteRollsOut.push('LL');
              lightSidePoints += 2;
              break;
          }
        }

        let rollFormula = `${nWhite} force = ${darkSidePoints} dark + ${lightSidePoints} light`;

        let fields = []
        if (this.verboseMode) fields = [{ name: "Rolls", value: whiteRollsOut.toString() }]

        this.buildAndPushPayload(rollFormula, fields);
      },
      buildPayload: function (rollFormula, fields) {
        let title = this.rollContext !== '' ? `${this.userNick} rolled ${this.rollContext}: ${rollFormula}` : `${this.userNick} rolled: ${rollFormula}`

        return {
          embeds: [
            {
              title: title,
              fields: fields
            }
          ]
        }
      },
      buildAndPushPayload: function (rollFormula, fields) {
        fields.push({ name: 'Roll Type', value: this.selectedType})
        const payload = this.buildPayload(rollFormula, fields);
        this.pushPayload(payload);
      },
      shiverRoll: function (nSkill, nTalent, rollFormula) {
        let strange = 0;
        let grit = 0;
        let wit = 0;
        let heart = 0;
        let smarts = 0;
        let luck = 0;
        let talent = 0;

        let skillRollsOut = [];
        let talentRollsOut = [];
        let fields = [];

        if (nSkill > 0) {
          for (let i = 0; i < nSkill; i++) {
            switch (this.generate(6)) {
              case 1:
                strange += 1;
                skillRollsOut.push(':purple_circle:');
                break;
              case 2:
                grit += 1;
                skillRollsOut.push(':orange_circle:');
                break;
              case 3:
                wit += 1;
                skillRollsOut.push(':blue_circle:');
                break;
              case 4:
                heart += 1;
                skillRollsOut.push(':red_circle:');
                break;
              case 5:
                smarts += 1;
                skillRollsOut.push(':yellow_circle:');
                break;
              case 6:
                luck += 1;
                skillRollsOut.push(':green_circle:');
                break;
            }
          }

          if (this.verboseMode) fields.push({ name: 'Skill Rolls', value: skillRollsOut.toString()})
        }

        if (nTalent > 0) {
          for (let i = 0; i < nTalent; i++) {
            switch (this.generate(8)) {
              case 1:
                strange += 2;
                talentRollsOut.push(':purple_circle: :purple_circle:');
                break;
              case 2:
              case 3:
                strange++;
                talentRollsOut.push(':purple_circle:');
                break;
              case 4:
              case 5:
              case 6:
                talent++;
                talentRollsOut.push(':white_circle:');
                break;
              case 7:
              case 8:
                talent += 2;
                talentRollsOut.push(':white_circle: :white_circle:');
                break;
            }
          }

          if (this.verboseMode) fields.push({ name: 'Talent Rolls', value: talentRollsOut.toString()})
        }

        let result = '';

        result = this.expandResultFormula(result, `${strange} :purple_circle:`)
        result = this.expandResultFormula(result, `${grit + talent} :orange_circle:`)
        result = this.expandResultFormula(result, `${wit + talent} :blue_circle:`)
        result = this.expandResultFormula(result, `${heart + talent} :red_circle:`)
        result = this.expandResultFormula(result, `${smarts + talent} :yellow_circle:`)
        result = this.expandResultFormula(result, `${luck + talent} :green_circle:`)

        let out = `${rollFormula} = ${result}`

        this.buildAndPushPayload(out, fields);
      }
    }
  }
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    margin-top: 60px;
  }

  * {
    background-color: #2c3e50;
    color: #CFF7FF;
    justify-content: center;
    align-content: center;
  }

  .top-selects {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  }

  button:focus {
    outline: none;
    box-shadow: none;
  }

  .title:hover {
    color: white;
    cursor: default;
  }

  .icon {
    background-image: url("img/icon-smaller.png");
    background-repeat: no-repeat;
    background-size: auto;
    height: 256px;
    background-position: center;
  }
</style>
