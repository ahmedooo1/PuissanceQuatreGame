<template>
  <div class="min-h-screen flex flex-col items-center justify-center bg-gradient-to-r from-purple-600 to-blue-600">
    <button class="inline-block rounded-full bg-green-500 px-6 pb-2 pt-2.5 text-xs font-medium uppercase leading-normal text-white shadow-[0_4px_9px_-4px_#14a44d] transition duration-150 ease-in-out hover:bg-green-600 hover:shadow-[0_8px_9px_-4px_rgba(20,164,77,0.3),0_4px_18px_0_rgba(20,164,77,0.2)] focus:bg-green-600 focus:shadow-[0_8px_9px_-4px_rgba(20,164,77,0.3),0_4px_18px_0_rgba(20,164,77,0.2)] focus:outline-none focus:ring-0 active:bg-green-700 active:shadow-[0_8px_9px_-4px_rgba(20,164,77,0.3),0_4px_18px_0_rgba(20,164,77,0.2)] dark:shadow-[0_4px_9px_-4px_rgba(20,164,77,0.5)] dark:hover:shadow-[0_8px_9px_-4px_rgba(20,164,77,0.2),0_4px_18px_0_rgba(20,164,77,0.1)] dark:focus:shadow-[0_8px_9px_-4px_rgba(20,164,77,0.2),0_4px_18px_0_rgba(20,164,77,0.1)] dark:active:shadow-[0_8px_9px_-4px_rgba(20,164,77,0.2),0_4px_18px_0_rgba(20,164,77,0.1)]" @click="reload">
      Rejouer
    </button>
    <h1 class="text-3xl p-2 text-center font-bold text-white">
      Puissance Quatre
    </h1>
    <div class="flex flex-row justify-center item-center p-1 bg-white rounded-full m-2">
      <p class="p-2 text-white rounded-full bg-yellow-500">
        Joueur 1
      </p>
      <p class="p-2">
        VS
      </p>
      <p class="p-2 text-white rounded-full bg-green-500">
        Joueur 2
      </p>
    </div>
    <div v-if="winner && winner === 1" class="text-white text-2xl font-bold bg-yellow-500 rounded-full p-2 flex justify-center item-center m-1">
      Le joueur {{ winner }} gagne !
    </div>
    <div v-else-if="winner && winner === 2" class="text-white text-2xl font-bold bg-green-500 rounded-full p-2 flex justify-center item-center m-1">
      Le joueur {{ winner }} gagne !
    </div>
    <table class="puissance-quatre  bg-white">
      <tbody class="divide-y divide-x divide-gray-700">
        <tr v-for="(row, rowIndex) in grid" :key="rowIndex">
          <td
            v-for="(cell, columnIndex) in row"
            :key="columnIndex"
            :class="{
              'bg-yellow-500': cell === 1,
              'bg-green-500': cell === 2,
              'border-l-2': columnIndex === 0,
              'border-r-2': columnIndex === grid[0].length - 1,
            }"
            class="w-16 h-16 cursor-pointer border-2 rounded-full"
            @click="play(columnIndex)"
          />
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'PuissanceQuatre',
  data () {
    return {
      player: 1,
      grid: Array(6)
        .fill()
        .map(() => Array(7).fill(0)),
      winner: null
    }
  },
  methods: {
    reload () { // pour rejouer
      window.location.reload()
    },
    play (column) {
      // terminer le joue si la partie est fini:
      if (this.winner) {
        return
      }
      // trouver le premiere case vide:
      const row = this.grid
        .slice()
        .reverse()
        .findIndex(r => r[column] === 0)
      // empecher de jouer si la case est pleine ou  choisi par l'autre joueur:
      if (row === -1) {
        return
      }
      // choisir la case vide et ajouter un jeton
      this.$set(this.grid[5 - row], column, this.player)
      // verifie si le joueur est gagné:
      if (this.checkVictory(5 - row, column)) {
        this.winner = this.player
      } else {
        this.player = 3 - this.player // passer à l'autre joueur
      }
    },
    checkVictory (row, column) {
      const player = this.player // joueur actuel
      const grid = this.grid // le grid de jeu
      const rowCount = grid.length // le nombre de rows
      const colunmCount = grid[0].length // les colons

      // vérifier l'alignement horizontal
      let count = 0 // le nombre de jeton aligné par defaut
      for (let i = 0; i < rowCount; i++) {
        // parcourt chaque ligne(row) du tableau
        count = 0 // notre compteur est 0 par defaut
        for (let j = 0; j < colunmCount; j++) {
          // c pour parcourir chaque column de notre ligne actuel
          if (grid[i][j] === player) {
            // si le jeton apartient au joueur actuel
            count++ // incremente le compteur
            if (count === 4) {
              // si notre compteur atteint 4
              return true // notre joueur gagne
            }
          } else {
            count = 0
          }
        }
      }
      // la verification vertical
      count = 0 // réinitialiser notre compteur
      // parcourt chaque column du tableau
      for (let i = 0; i < colunmCount; i++) {
        count = 0
        // parcourt chaque ligne(row) de notre tableau
        for (let j = 0; j < rowCount; j++) {
          // si le jeton appartient au joueur
          if (grid[j][i] === player) {
            count++
            if (count === 4) {
              return true
            }
          } else {
            count = 0
          }
        }
      }
      // verification diagonale vers le bas-droit et le haut-gauche
      count = 0 // reinitialise notre compteur à 0
      // parcourt le diagonale vers le bas-droit et le haut-gauche depuis notre position actuelle
      for (let i = row, j = column; i >= 0 && j >= 0; i--, j++) {
        if (grid[i][j] === player) {
          count++
          if (count === 4) {
            return true
          }
        } else {
          break // arreter le boucle si il ne correspond pas au joueur actuel
        }
      }
      // parcourt le diagonale vers le bas-droit et le haut-gauche depuis notre position actuelle
      for (let i = row + 1, j = column + 1; i < rowCount && j < colunmCount; i++, j++) {
        if (grid[i][j] === player) {
          count++
          if (count === 4) {
            return true
          }
        } else {
          break // arreter le boucle si il ne correspond pas au joueur actuel
        }
      }
      // verification diagonale vers le bas-droit et le haut-gauche
      count = 0
      for (let i = row, j = column; i < rowCount && j >= 0; i++, j--) {
        if (grid[i][j] === player) {
          count++
          if (count === 4) {
            return true
          }
        } else {
          break // arreter le boucle si il ne correspond pas au joueur actuel
        }
      }
      for (let i = row - 1, j = column + 1; i >= 0 && j < colunmCount; i--, j++) {
        if (grid[i][j] === player) {
          count++
          if (count === 4) {
            return true
          }
        } else {
          break // arreter le boucle si il ne correspond pas au joueur actuel
        }
      }

      return false // si aucun alignement n'a été trouvé!
    }
  }
}
</script>

<style scoped>
.puissance-quatr td {
  border: 2px solid black;
}
</style>
