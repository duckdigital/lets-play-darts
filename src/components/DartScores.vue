<template>
  <h1>Lets Play Darts!</h1>
  <p>Decide starting score - Add players,hit play, add players score</p>
  <form class="d-flex">
    <div class="d-flex">
      <label for="startingScore">Starting score: </label>
      <input type="number" id="startingScore" v-model="startingScore" />
    </div>
    <div class="d-flex">
      <label for="newPlayerName"> Player name: </label>
      <input id="newPlayerName" v-model="newPlayer" />
    </div>
    <button @click.prevent="addPlayer()">Add player</button>
  </form>

  <h2>Players</h2>
  <ul class="players-list">
    <template v-for="(player, index) in players" :key="index">
      <li class="player">
        <h3>{{ player.playerName }}</h3>
        <form class="score-controls">
          <label
            >Score: <input type="number" v-model="player.roundScore" /></label
          ><button @click.prevent="updatePlayerScore(index)">add</button>
          <button
            type="button"
            title="undo last score"
            @click.prevent="undoScore(index)"
          >
            undo
          </button>
        </form>
        <div class="player-scores">
          <h4>Remaining: {{ player.score }}</h4>
          <p v-if="isOutShot(player.score)">Outshot: {{ playerOutshot(player.score) }}</p>
          <ol class="round-scores">
            <li v-for="score in player.roundScores">{{ score }}</li>
          </ol>
        </div>
      </li>
    </template>
  </ul>
  <button type="button" @click="resetScores()">Reset scores</button>
  <button type="button" @click="clearPlayers()">Clear Players</button>
</template>

<script setup>
import { ref, computed } from 'vue';
import { outshots } from '../data/outshots.js';

const startingScore = ref(501);

const newPlayer = ref('');

const players = ref([]);

function playerOutshot(scoreRemaining){
  let newOutshot = outshots.find((outshot) => outshot.score === scoreRemaining);
  return newOutshot.outshot;
};

function isOutShot(scoreRemaining) {
  return outshots.find((outshot) => outshot.score === scoreRemaining);
}

const addPlayer = () => {
  players.value.push({
    playerName: newPlayer.value,
    score: startingScore.value,
    roundScore: 0,
    roundScores: [],
  });
  newPlayer.value = '';
};

const updatePlayerScore = (index) => {
  if (players.value[index].roundScore > 180) {
    alert('You cannot score more than 180 with 3 darts!');
    players.value[index].roundScore = 0;
    return;
  }
  if ((players.value[index].score - players.value[index].roundScore) < 0 || (players.value[index].score - players.value[index].roundScore) === 1) {
    alert('bust!');
    players.value[index].roundScore = 0;
    return;
  };
  players.value[index].score =
    players.value[index].score - players.value[index].roundScore;
  players.value[index].roundScores.push(players.value[index].roundScore);
  players.value[index].roundScore = 0;
};

const undoScore = (index) => {
  players.value[index].score =
    players.value[index].score + players.value[index].roundScores.at(-1);
  players.value[index].roundScores.pop();
};

const resetScores = () => {
  players.value.forEach((player) => {
    player.score = startingScore.value;
    player.roundScores = [];
  });
};

const clearPlayers = () => {
  players.value = [];
};
</script>
<style lang="css">
h1,
h2,
h3,
h4,
h5,
h6 {
  font-family: 'Permanent Marker', cursive;
  font-weight: 400;
  font-style: normal;
}
.d-flex {
  display: flex;
}
.players-list {
  display: flex;
  justify-content: space-around;
  list-style-type: none;
  padding: 0;
  font-family: 'Permanent Marker', cursive;
  font-weight: 400;
  font-style: normal;
  gap: 1.5rem;
  li {
    h4 {
      border-bottom: 2px solid #fff;
    }
    margin: 0;
    .round-scores {
      padding: 1.5rem;
      li {
        margin: 0 0 1rem 0;
      }
    }
  }
}
</style>
