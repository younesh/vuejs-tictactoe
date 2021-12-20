<template>
  <div class="container">
    <div class="wrapper-morpion">
      <b-row class="grille-morpion row no-gutters">
        <b-col cols="6">
          <div class="score win-X">
            score X: <br />
            {{ score_player }}
          </div>
        </b-col>
        <b-col cols="6">
          <div class="score win-O">
            score O:<br />
            {{ score_pc }}
          </div>
        </b-col>
        <b-col cols="4">
          <input
            class="cell"
            id="cell-0"
            data-id="0"
            readonly
            v-model="grille[0]"
            @click="play($event)"
          />
        </b-col>
        <b-col cols="4">
          <input
            class="cell"
            id="cell-1"
            data-id="1"
            readonly
            v-model="grille[1]"
            :key="1"
            @click="play($event)"
          />
        </b-col>
        <b-col cols="4">
          <input
            class="cell"
            id="cell-2"
            data-id="2"
            readonly
            v-model="grille[2]"
            :key="2"
            @click="play($event)"
          />
        </b-col>
        <b-col cols="4">
          <input
            class="cell"
            id="cell-3"
            data-id="3"
            readonly
            v-model="grille[3]"
            :key="3"
            @click="play($event)"
          />
        </b-col>
        <b-col cols="4">
          <input
            class="cell"
            id="cell-4"
            data-id="4"
            readonly
            v-model="grille[4]"
            :key="4"
            @click="play($event)"
          />
        </b-col>
        <b-col cols="4">
          <input
            class="cell"
            id="cell-5"
            data-id="5"
            readonly
            v-model="grille[5]"
            :key="5"
            @click="play($event)"
          />
        </b-col>
        <b-col cols="4">
          <input
            class="cell"
            id="cell-6"
            data-id="6"
            readonly
            v-model="grille[6]"
            :key="6"
            @click="play($event)"
          />
        </b-col>
        <b-col cols="4">
          <input
            class="cell"
            id="cell-7"
            data-id="7"
            readonly
            v-model="grille[7]"
            :key="7"
            @click="play($event)"
          />
        </b-col>
        <b-col cols="4">
          <input
            class="cell"
            id="cell-8"
            data-id="8"
            readonly
            v-model="grille[8]"
            :key="8"
            @click="play($event)"
          />
        </b-col>
      </b-row>
    </div>
    <div class="annonce-winner">
      <template v-if="gameOver">
        The player {{ theWinnerParty }} win !!!
      </template>
    </div>

    <button @click="initGame" class="btn btn-success mt-3">New game</button>
  </div>
</template>

<script>
export default {
  name: "GrilleMorpion",
  data() {
    return {
      grille: {
        0: "",
        1: "",
        2: "",
        3: "",
        4: "",
        5: "",
        6: "",
        7: "",
        8: "",
      },
      playerSymbole: "X",
      pcSymbole: "O",
      gameOver: false,
      score_player: 0,
      score_pc: 0,
      freezPlayer: false,
      theWinnerParty: "",
    };
  },
  watch: {
    grille: {
      handler() {
        if (this.isPlayerWin(this.playerSymbole)) {
          this.score_player++;
          this.gameOver = true;
          this.theWinnerParty = this.playerSymbole;
        }
        if (this.isPlayerWin(this.pcSymbole)) {
          this.score_pc++;
          this.gameOver = true;
          this.theWinnerParty = this.pcSymbole;
        }
      },
      deep: true,
    },
  },
  methods: {
    async play(event) {
      if (event) {
        let currentID = Number(event.currentTarget.dataset.id);
        if (
          this.grille[currentID] !== "" ||
          this.gameOver ||
          this.freezPlayer
        ) {
          return;
        }
        this.grille[currentID] = this.playerSymbole;

        // play a PC move !
        let emptyKeys = [];
        Object.keys(this.grille).forEach((elm) => {
          if (this.grille[elm] === "") {
            emptyKeys = [...emptyKeys, Number(elm)];
          }
        });

        let nextPcMoveIndex =
          emptyKeys[Math.floor(Math.random() * emptyKeys.length)];

        this.freezPlayer = true;
        setTimeout(() => {
          if (!this.gameOver) {
            this.grille[nextPcMoveIndex] = this.pcSymbole;
            this.freezPlayer = false;
          }
        }, 1000);
      }
    },
    isPlayerWin(playerSymbole) {
      const allLigneToCheck = [
        [0, 1, 2],
        [0, 3, 6],
        [0, 4, 8],
        [3, 4, 5],
        [6, 7, 8],
        [6, 4, 2],
        [1, 4, 7],
        [2, 5, 8],
      ];

      for (const ligne of allLigneToCheck) {
        if (this.checkLigneSuccess(ligne, playerSymbole)) {
          return true;
        }
      }
      return false;
    },
    checkLigneSuccess(ligne, playerSymbole) {
      // check if a ligne of grid is sucess
      if (ligne.every((nbr) => this.grille[nbr] === playerSymbole)) {
        // marque ligne with color player
        for (let elm of ligne) {
          document
            .getElementById("cell-" + elm)
            .classList.add("win-" + playerSymbole);
        }
        this.gameOver = true;
        return true;
      }
      return false;
    },
    initGame() {
      this.grille = {
        0: "",
        1: "",
        2: "",
        3: "",
        4: "",
        5: "",
        6: "",
        7: "",
        8: "",
      };
      this.gameOver = false;
      this.freezPlayer = false;
      document.querySelectorAll(".cell").forEach((elm) => {
        elm.classList.remove("win-X");
        elm.classList.remove("win-O");
      });
    },
  },
  created() {
    this.initGame();
  },
};
</script>

<style lang="scss">
$color-X: green;
$color-O: orange;
.wrapper-morpion {
  width: 100%;
  display: flex;
  justify-content: center;
}
.grille-morpion {
  position: relative;
  width: 320px;
  height: 320px;
  margin: 0 auto;
  .cell {
    height: 96%;
    width: 96%;
    background-color: palegoldenrod;
    margin-bottom: 5px;
    appearance: none;
    border: none;
    text-align: center;
    cursor: pointer;
    border-radius: 5px;
    font-weight: bold;
    font-size: 30px;
    outline: none;
    &.win-X {
      background-color: $color-X;
    }
    &.win-O {
      background-color: $color-O;
    }
  }
  .score {
    background-color: palegoldenrod;
    height: 96%;
    width: 96%;
    font-size: 23px;
    border-radius: 5px;
  }
}
.annonce-winner {
  color: green;
  font-size: 30px;
  height: 45px;
}
</style>
