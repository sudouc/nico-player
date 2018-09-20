<template>
  <div id="app">

    <div class="player" v-for="player in players">Player: {{ player }}</div>
  </div>
</template>

<script>
import client, { Player, World } from "nico-client";
import Vue from "vue";

export default {
  name: "app",
  data() {
    return {
      world: {},
      players: []
    };
  },
  methods: {
    updateWorld(newPlayerData) {
      this.players.forEach((player, index) => {
        if (newPlayerData._id === player._id) {
        	this.$set(this.players, index, newPlayerData)

        }
      });
    }
  },
  mounted: function() {
    client.setup('https://nico.sudo.org.au')
    this.world = new World(client.service('players'));
    this.world.getPlayers(players => (this.players = players));
    this.world.onUpdate(this.updateWorld);
  }
};
</script>

<style lang="scss">
html,
body {

}
</style>
