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
