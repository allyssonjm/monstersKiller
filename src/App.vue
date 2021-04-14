<template>
  <div id="app">
    <div class="panel scores">
      <div class="score">
        <h1>Jogador</h1>
        <div class="life-bar">
          <div
            class="life"
            :class="{ danger: playerLife < 20 }"
            :style="{ width: playerLife + '%' }"
          ></div>
        </div>
        <div>{{ playerLife }}%</div>
      </div>

      <div class="score">
        <h1>Monstro</h1>
        <div class="life-bar">
          <div
            class="life"
            :class="{ danger: monsterLife < 20 }"
            :style="{ width: monsterLife + '%' }"
          ></div>
        </div>
        <div>{{ monsterLife }}%</div>
      </div>
    </div>

    <div v-if="hasResult" class="panel result">
      <div class="result">
        <div v-if="playerLife > 0 && monsterLife == 0" class="win">
          <p>Você ganhou! :)</p>
        </div>
        <div v-else class="lose">
          <p>Você perdeu! :(</p>
        </div>
      </div>
    </div>

    <div class="panel buttons">
      <template v-if="running">
        <button class="btn atack" @click="atack(false)">Ataque</button>
        <button class="btn especial-atack" @click="atack(true)">
          Ataque Especial
        </button>
        <button class="btn health" @click="health(true)">Curar</button>
        <button class="btn give-up" @click="restart">
          Desistir
        </button>
      </template>
      <button v-else class="btn start" @click="start">Iniciar jogo</button>
    </div>
    <div v-if="logs.length" class="panel logs">
      <ul>
        <li v-for="(log, index) in logs" class="log" :key="index">
          {{ log.text }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      running: false,
      playerLife: 100,
      monsterLife: 100,
      logs: []
    }
  },
  computed: {
    hasResult() {
      return this.playerLife == 0 || this.monsterLife == 0
    }
  },
  methods: {
    getRandom(min, max) {
      const value = (Math.random() * (max - min)) + min
      return Math.round(value)
    },
    start() {
      this.running = true
      this.playerLife = 100
      this.monsterLife = 100
      this.logs = []
    },
    restart() {
      this.running = false
      this.playerLife = 100
      this.monsterLife = 100
      this.logs = []
    },
    atack(especial) {
      this.strength('monsterLife', 6, 10, especial, 'Jogador', 'Monstro', 'player')
      if (this.monsterLife > 0) {
        this.strength('playerLife', 7, 12, false, 'Monstro', 'Jogador', 'monster')
      }
    },
    strength(character, min, max, especial, source, target, classe) {
      const plus = especial ? 5 : 0
      const powerAtack = this.getRandom(min + plus, max + plus)
      this[character] = Math.max(this[character] - powerAtack, 0)
      this.registerLog(`${source} atingiu ${target} com ${this.strength}.`, classe)
    },
    //Qual a necessidade do boolean heal_ se é sempre true?
    health(heal_) {
      this.heal('playerLife', 1, 3, heal_)
    },
    //Qual a necessidade de escolher o playerlife se é sempre o mesmo
    heal(character, min, max, heal_) {
      if (this[character] === 100) return
      const addHealth = heal_ ? 5 : 0
      const heal = this.getRandom(min + addHealth, max + addHealth)
      this[character] = Math.min(this[character] + heal, 100)
      this.strength('playerLife', 3, 10, false)
      this.registerLog(`Jogador aumentou a vida em ${heal}.`, 'player')
    },
    registerLog(text, classe) {
      this.logs.unshift({ text, classe })
    }
  },
  watch: {
    hasResult(value) {
      if (value == true) {
        this.running = false
      }
    },
  }
}
</script>

<style>
/*Geral*/

html {
  font-family: "Montserrat", sans-serif;
}

#app {
  display: flex;
  flex-direction: column;
}

.panel {
  margin: 10px;
  padding: 20px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.15);
}

/*Scores*/

.scores {
  display: flex;
}

.score {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.score h1 {
  font-weight: 300;
  font-size: 1.6rem;
}

.life-bar {
  width: 80%;
  height: 20px;
  border: 1px solid #aaa;
}

.life-bar .life {
  display: flex;
  justify-content: center;
  height: 100%;
  background-color: green;
}

.life-bar .life.danger {
  background-color: red;
}

/*Result*/

.result {
  display: flex;
  justify-content: center;
  font-weight: 600;
  font-size: 1.6rem;
}

.result .win {
  color: green;
}

.result .lose {
  color: red;
}

/*Buttons*/

.buttons {
  display: flex;
  justify-content: center;
}

.btn {
  padding: 5px 10px;
  margin: 0px 7px;
  border-radius: 10px;
  text-transform: uppercase;
  font-size: 1rem;
}

.start {
  background-color: blue;
  color: white;
}

.start:hover {
  background-color: rgba(0, 0, 255, 0.8);
  color: white;
}

.atack {
  background-color: red;
  color: white;
}

.atack:hover {
  background-color: rgba(255, 0, 0, 0.8);
  color: white;
}

.especial-atack {
  background-color: red;
  color: white;
}

.especial-atack:hover {
  background-color: rgba(255, 0, 0, 0.8);
  color: white;
}

.health {
  background-color: green;
  color: white;
}

.health:hover {
  background-color: rgba(0, 128, 0, 0.8);
  color: white;
}

.give-up {
  background-color: blue;
  color: white;
}

.give-up:hover {
  background-color: rgba(0, 0, 255, 0.8);
  color: white;
}

/*Logs*/

.logs ul {
  display: flex;
  flex-direction: column;
  list-style: none;
  padding: 0px;
  margin: 0px;
}

.logs ul li {
  display: flex;
  justify-content: center;
  margin: 4px 0px;
  padding: 3px 0px;
  font-weight: 600;
  font-size: 1rem;
  text-transform: uppercase;
  border-radius: 5px;
}

.player {
  background-color: #4243afaa;
  color: white;
}

.bot {
  background-color: #e51c23aa;
  color: white;
}
</style>
