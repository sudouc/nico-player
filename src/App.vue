<template>
  <div id="app">

    <h3>Current Player</h3>
    {{ currentPlayer }}

    <hr>
    <div class="playerClass" v-for="player in players">
      Player ID: {{ player._id }}<br>
      Player X: {{ player.location.x }}<br>
      Player Y: {{ player.location.y }}<br><br>

      <input v-on:keyup.up="submit">
    </div>
  </div>
</template>

<script>
import client, { Player, World } from "nico-client";
import Vue from "vue";

export default {
  name: "app",
  data() {
    return {
      currentPlayer: new Player(),
      world: {},
      players: []
    };
  },
  methods: {
    updateWorld(newPlayerData) {
      this.players.forEach((player, index) => {
        if (newPlayerData._id === player._id) {
          this.$set(this.players, index, newPlayerData);
        }
      });
    }
  },
  mounted: function() {
    client.setup("https://nico.sudo.org.au");
    this.world = new World(client.service("players"));
    this.world.getPlayers(response => (this.players = response));
    this.world.onUpdate(this.updateWorld);

    // Add player to the current world
    this.currentPlayer.addToWorld(this.world);

    const playerID = "Example";
    this.currentPlayer.setPlayerID(playerID);

    this.currentPlayer.existsInWorld().then(exists => {
      exists ? "" : this.currentPlayer.createInWorld();
    });
  }
};
</script>

<style lang="scss">
html,
body {
}
</style>
