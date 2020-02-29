<template>
  <v-app>
    <v-content>      
      <div v-if="_showNoRaffled()">Nenhum sorteio realizado</div>
      <v-row v-else>
        <Number :picked="numberRaffled(number)" v-for="(number, index) in raffledNumbers" :key="index" :value="number" />
      </v-row> 

      <v-row>
        <v-btn color="primary" :disabled="loading" @click="_raffling()">Iniciar Sorteio</v-btn>
        <v-btn color="primary" :disabled="loading" @click="_generateRandomGames(3)">Gerar Novos Números</v-btn>
      </v-row>

      <v-divider></v-divider>

      <h1>Meus Jogos</h1>

      <v-row v-for="(game, indexGame) in games" :key="indexGame">
        <h1>{{indexGame + 1}}: {{' '}}</h1>
        <Number :picked="numberRaffled(number)" v-for="(number, indexNumber) in game" :key="indexNumber" :value="number" />
      </v-row>     


      <v-divider></v-divider>

      <v-row>
        <new-game-dialog
          :disabled="loading"
          @newGameAdded="addNewGame($event)">
        </new-game-dialog>
      </v-row>
    </v-content>
  </v-app>
</template>

<script>
import { randomNumbers } from '../src/helpers/randomHelpers';
import { sort } from '../src/helpers/sortHelper';
import { interval } from 'rxjs';
import { map, take } from 'rxjs/operators';
import Number from './components/number/Number';
import NewGameDialog from './components/game/NewGameDialog';

export default {
  name: 'App',

  components: {
    NewGameDialog,
    Number
  },
  created() {
    this._generateRandomGames(5);
  },
  methods: {
    randomNumbers,
    _showNoRaffled: function() {
      return !this.loading && this.raffledNumbers.length === 0;
    },
    numberRaffled: function(number) {
      return this.raffledNumbers.some(x => x === number);
    },
    addNewGame: function(newGame) {
      this.raffledNumbers = [];
      sort(newGame);
      this.games.push(newGame);
    },
    _generateRandomGames(numberOfGames = 3) {
      this.raffledNumbers = [];
      this.games = [];

      for (let i = 0; i < numberOfGames; i++) {
        this.addNewGame(randomNumbers(1, 60, 6));
      }
    },
    _raffling() {
      this.loading = true;
      this.raffledNumbers = [];

      const game = randomNumbers(1, 60, 6);

      interval(500).pipe(
        take(game.length),
        map(index => game[index])).subscribe(
          number => {
            this.raffledNumbers.push(number);
            this.$emit('raffling', this.raffledNumbers);
          },
          error => {
            console.error(error);
          },
          () => {
            this.loading = false;
          }
          );
    }
  },
  data: () => ({
    games: [],
    raffledNumbers: [],
    loading: false
  }),
};
</script>

<style>
body {
  padding: 20px;
}
</style>