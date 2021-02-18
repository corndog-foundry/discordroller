<template>
  <div>
    <form @submit.prevent="submitKey">
      <p>Enter your Webhook URL here:</p>
      <input type="text" v-model="newKey"/>

      <p>Enter a nickname here:</p>
      <input type="text" v-model="newNick"/>
      <button>Submit</button>
    </form>
  </div>
</template>

<script>
  export default {
    name: "GetKey",
    data () {
      return {
        newKey: '',
        newNick: ''
      }
    },
    methods: {
      submitKey: function () {
        this.$emit('change:DiscordKey', this.newKey, this.newNick);
      }
    }
  }
</script>

<style scoped>

</style>
