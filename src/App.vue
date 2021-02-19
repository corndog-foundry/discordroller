<template>
  <div id="app">
    <h1>Discord Roller</h1>
    <div v-if="keyInput">
      <select v-model="selectedType">
        <option v-for="type in diceTypes" :key="type">{{ type }}</option>
      </select>

      <hr>

      <Roller
          v-if="selectedType === 'Standard'"
          @event:Roll="standardRoll"
          @revert:DiscordKey="revertDiscordKey"
      />

      <StarWarsRoller
        v-if="selectedType === 'Star Wars / Genesys'"
        @event:StandardRoll="starWarsRoll"
        @event:ForceRoll="starWarsForceRoll"
      />

      <VampireRoller
        v-if="selectedType === 'Vampire the Masquerade 5e'"
        @event:Roll="vampireRoll"
      />
    </div>
    <GetKey
        v-else
        @change:DiscordKey="receiveDiscordKey"
    />
  </div>
</template>

<script>
  import GetKey from "@/components/GetKey";
  import Roller from "@/components/Roller";
  import StarWarsRoller from "@/components/StarWarsRoller";
  import VampireRoller from "@/components/VampireRoller";
  export default {
    name: 'App',
    components: {
      VampireRoller,
      StarWarsRoller,
      Roller,
      GetKey
    },
    data () {
      return {
        discordKey: '',
        keyInput: false,
        userNick: '',
        diceTypes: [
            'Standard',
            'Star Wars / Genesys',
            'Vampire the Masquerade 5e'
        ],
        selectedType: 'Standard'
      }
    },
    methods: {
      receiveDiscordKey: function (newKey, newNick) {
        this.discordKey = newKey;
        this.userNick = newNick;
        this.keyInput = true;
      },
      standardRoll: function (numDice, numSides, bonus) {
        let result = parseInt(bonus);
        let rolls = [];

        for (let i = 0; i < numDice; i++) {
          let roll = this.generate(numSides);
          rolls.push(roll);
          result += roll;
        }

        let rollFormula = `${numDice}d${numSides}`;
        if (bonus > 0) rollFormula += ` + ${bonus}`;
        else if (bonus < 0) rollFormula += ` - ${Math.abs(bonus)}`;

        rollFormula += ` = ${result}`

        const payload = {
          embeds: [
            {
              title: `${this.userNick} rolled: ${rollFormula}`,
              fields: [
                {
                  name: "Rolls",
                  value: rolls.toString()
                }
              ]
            }
          ]
        };

        this.pushPayload(payload);
      },
      vampireRoll: function (numBlackDice, numRedDice) {
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

        let rollFormula = '';

        if (numRedDice > 0 && numBlackDice > 0) rollFormula = `${numBlackDice} black + ${numRedDice} red = `
        else if (numBlackDice > 0) rollFormula = `${numBlackDice} black = `
        else if (numRedDice > 0) rollFormula = `${numRedDice} red = `

        rollFormula += `${successes} successes`

        const fields = [];

        if (numBlackDice > 0) fields.push({name: "Black Rolls", value: blackRollsOut.toString()})
        if (numRedDice > 0) fields.push({name: "Red Rolls", value: redRollsOut.toString()})

        if (messyCritical) fields.push({name: "Messy Critical?", value: "Yes"})
        if (bestialFailure) fields.push({name: "Bestial Failure?", value: "Yes"})

        const payload = {
          embeds: [
            {
              title: `${this.userNick} rolled: ${rollFormula}`,
              fields: fields
            }
          ]
        };

        this.pushPayload(payload);
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
      starWarsRoll: function (nBlue, nGreen, nYellow, nBlack, nPurple, nRed) {
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

        let rollFormula = '';

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

          fields.push({name: "Boost Rolls", value: blueRollsOut.toString()})
          rollFormula = this.expandRollFormula(rollFormula, `${nBlue} blue`);
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
                blueRollsOut.push('T');
                advantages--;
                break;
            }
          }

          fields.push({name: "Setback Rolls", value: blackRollsOut.toString()})
          rollFormula = this.expandRollFormula(rollFormula, `${nBlack} black`);
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

          fields.push({name: "Ability Rolls", value: greenRollsOut.toString()})
          rollFormula = this.expandRollFormula(rollFormula, `${nGreen} green`);
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

          fields.push({name: "Difficulty Rolls", value: purpleRollsOut.toString()})
          rollFormula = this.expandRollFormula(rollFormula, `${nPurple} purple`);
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

          fields.push({name: "Proficiency Rolls", value: yellowRollsOut.toString()})
          rollFormula = this.expandRollFormula(rollFormula, `${nYellow} yellow`);
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

          fields.push({name: "Challenge Rolls", value: redRollsOut.toString()})
          rollFormula = this.expandRollFormula(rollFormula, `${nRed} red`);
        }

        if (triumph) fields.push({name: "Triumph?", value: "Yes"});
        if (despair) fields.push({name: "Despair?", value: "Yes"});

        let sResult = successes > 0 ? `${successes} successes` : `${Math.abs(successes)} failures`;
        let aResult = advantages > 0 ? `${advantages} advantages` : `${Math.abs(advantages)} threats`;

        rollFormula += ` = ${sResult} + ${aResult}`;

        const payload = {
          embeds: [
            {
              title: `${this.userNick} rolled: ${rollFormula}`,
              fields: fields
            }
          ]
        };

        this.pushPayload(payload);
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

        const payload = {
          embeds: [
            {
              title: `${this.userNick} rolled: ${rollFormula}`,
              fields: [
                {
                  name: "Rolls",
                  value: whiteRollsOut.toString()
                }
              ]
            }
          ]
        };

        this.pushPayload(payload);
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
  color: #2c3e50;
  margin-top: 60px;
}
</style>
