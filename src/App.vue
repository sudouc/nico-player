<template>
  <div id="app">

    <h3>Current Player</h3>

    {{ currentPlayer.location }}
    <input
            v-on:keydown.up="currentPlayer.moveUp()"
            v-on:keydown.down="currentPlayer.moveDown()"
            v-on:keydown.left="currentPlayer.moveLeft()"
            v-on:keydown.right="currentPlayer.moveRight()">

    <input type="text" v-model="currentPlayer.location.x">
    <input type="text" v-model="currentPlayer.location.y">
    <hr>
    <div class="playerClass" v-for="player in players">
      <div class="box" :class="{ me: player._id == 'Hello World' }" :style="{ bottom: player.location.y / 3 + 'vh', left: player.location.x / 3 + 'vh' }">{{ player._id }}</div>
      Player ID: {{ player._id }}<br>
      Player X: {{ player.location.x }}<br>
      Player Y: {{ player.location.y }}<br><br>


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

    const playerID = "Hello World";
    this.currentPlayer.setPlayerID(playerID);

    this.currentPlayer.existsInWorld().then(exists => {
      exists ? "" : this.currentPlayer.createInWorld();
    });
  }
};
</script>

<style lang="scss">
.box {
  position: fixed;
  border-radius: 100px;
  background-color: red;
  width: 20px;
  height: 20px;

  &.me {
    height: 50px;
    width: 50px;
    background-color: blue;
  }
}
</style>
