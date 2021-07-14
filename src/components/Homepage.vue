<template>
  <div>
    <h1>Minesweeper</h1>
    <div class="header">
      <button @click="reset()">Restart</button>
    </div>
    <div class="grid-container">
      <div
        @click="cellClick(cell)"
        class="grid-item"
        v-for="cell in matrix"
        :key="cell.key"
        :class="{ 'cell-open': cell.visited, 'black-border': cell.visited }"
      >
        {{ cell.visited ? cell.data : null }}
      </div>
    </div>
    l̥
  </div>
</template>

<script>
export default {
  data() {
    return {
      clicks: 0,
      matrix: [],
      grid: [],
      dirs: [
        [1, 0],
        [0, 1],
        [-1, 0],
        [0, -1],
      ],
      numbersIndexes: [],
      bombIndexes: [],
      ifLost: false,
    };
  },
  mounted() {
    this.reset();
  },
  methods: {
    reset() {
      this.clicks = 0;
      this.matrix = [];
      this.grid = [];
      this.numbersIndexes = [];
      this.bombIndexes = [];
      this.ifLost = false;
      this.generateGrid();
      //generate numbers
      this.initNumbers();
      //Generate bomb
      this.initBombs();
    },
    generateGrid() {
      let count = 0;
      for (let i = 0; i < 10; i++) {
        let row = [];
        for (let j = 0; j < 10; j++) {
          let data = {
            key: count,
            visited: false,
            row: i,
            column: j,
            data: null,
            type: "blank",
          };
          this.matrix.push(data);
          row.push(data);
          count++;
        }
        this.grid.push(row);
      }
    },
    initNumbers() {
      while (this.numbersIndexes.length < 40) {
        let row = this.randomIntFromInterval(0, 9);
        let col = this.randomIntFromInterval(0, 9);
        const cell = this.grid[row][col];
        if (!this.numbersIndexes.includes(cell)) {
          cell.data = this.randomIntFromInterval(0, 10);
          cell.type = "number";
          this.numbersIndexes.push(cell);
        }
      }
    },
    initBombs() {
      while (this.bombIndexes.length < 10) {
        let row = this.randomIntFromInterval(0, 9);
        let col = this.randomIntFromInterval(0, 9);
        const cell = this.grid[row][col];
        if (
          !this.bombIndexes.includes(cell) &&
          !this.numbersIndexes.includes(cell)
        ) {
          cell.type = "bomb";
          this.bombIndexes.push(cell);
        }
      }
    },
    cellClick(cell) {
      if (this.ifLost) {
        window.alert("Restart the game.");
        return;
      }

      if (this.clicks === 90) {
        window.alert("Win!!!");
        this.reset();
      }

      if (cell.type === "bomb") {
        cell.visited = true;
        this.ifLost = true;
        cell.data = "BOMB";
        this.bombIndexes.forEach((index) => {
          index.visited = true;
          index.data = "bomb";
        });
        window.alert("Lost it!");
      }

      if (!cell.isBlank && cell.data !== null) {
        cell.visited = true;
        return;
      }
      this.dfs(cell.row, cell.column);
    },
    dfs(row, col) {
      if (row >= 0 && row < 10 && col >= 0 && col < 10) {
        if (
          this.grid[row][col].type === "blank" &&
          !this.grid[row][col].visited
        ) {
          this.grid[row][col].visited = true;
          this.dirs.forEach((dir) => {
            this.dfs(row + dir[0], col + dir[1]);
            this.grid[row][col].visited = true;
          });
        }
      }
    },
    randomIntFromInterval(min, max) {
      return Math.floor(Math.random() * (max - min + 1) + min);
    },
  },
};
</script>

<style scoped>
.header {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 30px 10px;
  height: 50px;
}
button {
  height: 40px;
  width: 80px;
  background: white;
  border: 1px solid black;
}
button:hover {
  background: green;
  cursor: pointer;
  color: white;
}
h1 {
  text-align: center;
}
.grid-container {
  border: 2px solid black;
  width: 600px;
  margin: auto;
  display: grid;
  grid-template-columns: repeat(10, 60px);
  grid-template-rows: repeat(10, 55px);
}
.grid-item {
  display: flex;
  font-weight: 400;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  border: 1px solid white;
  background: black;
}
.black-border {
  border: 1px solid black;
}
.cell-open {
  background: none;
}
.grid-item:hover {
  background-color: red;
}
</style>