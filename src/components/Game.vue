<template>
  <div
    ref="gameContainer"
    class="game-container"
    @keydown="handleKeyDown"
    @keyup="handleKeyUp"
    tabindex="0"
  >
    <Spaceship :positionX="spaceshipX" />
    <Bullet
      v-for="(bullet, index) in bullets"
      :key="index"
      :positionX="bullet.x"
      :positionY="bullet.y"
    />
    <Asteroid
      v-for="(asteroid, index) in asteroids"
      :key="index"
      :positionX="asteroid.x"
      :positionY="asteroid.y"
    />
    <Enemy
      v-for="(enemy, index) in enemies"
      :key="index"
      :positionX="enemy.x"
      :positionY="enemy.y"
    />
    <div class="score">Score: {{ score }}</div>
    <div class="game-title">Space Shooter Game</div>
    <div class="lives">Lives: {{ lives }}</div>
    <div>
      <GameOverModalDialog
        :show="showModal"
        :score="score"
        @close="showModal = false"
        @restart="handleRestart"
      />
    </div>
  </div>
</template>

<script>
import Spaceship from "./Spaceship.vue";
import Bullet from "./Bullet.vue";
import Asteroid from "./Asteroid.vue";
import Enemy from "./Enemy.vue";
import GameOverModalDialog from "./GameOverModalDialog.vue";

export default {
  components: {
    Spaceship,
    Bullet,
    Asteroid,
    Enemy,
    GameOverModalDialog,
  },
  data() {
    return {
      spaceshipX: 200,
      bullets: [],
      asteroids: [],
      enemies: [],
      score: 0,
      lives: 3,
      isMovingLeft: false,
      isMovingRight: false,
      showModal: false,
      asteroidInterval: null,
      enemyInterval: null,
    };
  },
  mounted() {
    this.$el.focus();
    this.startGame();
  },
  beforeUnmount() {
    this.stopGame();
  },
  methods: {
    startGame() {
      this.gameLoop = setInterval(this.updateGame, 1000 / 60); // 60 FPS
      this.asteroidInterval = setInterval(this.spawnAsteroid, 2000);
      this.enemyInterval = setInterval(this.spawnEnemy, 3000);
    },
    stopGame() {
      clearInterval(this.gameLoop);
      clearInterval(this.asteroidInterval);
      clearInterval(this.enemyInterval);
    },
    handleKeyDown(event) {
      if (event.key === "ArrowLeft") this.isMovingLeft = true;
      if (event.key === "ArrowRight") this.isMovingRight = true;
      if (event.key === " ") this.shoot();
    },
    handleKeyUp(event) {
      if (event.key === "ArrowLeft") this.isMovingLeft = false;
      if (event.key === "ArrowRight") this.isMovingRight = false;
    },
    updateGame() {
      if (this.isMovingLeft) this.spaceshipX = Math.max(this.spaceshipX - 5, 0);
      if (this.isMovingRight)
        this.spaceshipX = Math.min(
          this.spaceshipX + 5,
          this.$el.clientWidth - 50
        );

      this.bullets.forEach((bullet, index) => {
        bullet.y -= 5;
        if (bullet.y < 0) this.bullets.splice(index, 1);
      });

      this.asteroids.forEach((asteroid, index) => {
        asteroid.y += 2;
        if (asteroid.y > this.$el.clientHeight) this.asteroids.splice(index, 1);
      });

      this.enemies.forEach((enemy, index) => {
        enemy.y += 3;
        if (enemy.y > this.$el.clientHeight) this.enemies.splice(index, 1);
      });

      this.checkCollisions();
    },
    shoot() {
      this.bullets.push({
        x: this.spaceshipX + 30,
        y: this.$el.clientHeight - 80,
      });
    },
    spawnAsteroid() {
      const x = Math.random() * (this.$el.clientWidth - 50);
      this.asteroids.push({ x, y: 0 });
    },
    spawnEnemy() {
      const x = Math.random() * (this.$el.clientWidth - 50);
      this.enemies.push({ x, y: 0 });
    },
    checkCollisions() {
      this.bullets.forEach((bullet, bulletIndex) => {
        this.asteroids.forEach((asteroid, asteroidIndex) => {
          if (this.isColliding(bullet, asteroid)) {
            this.bullets.splice(bulletIndex, 1);
            this.asteroids.splice(asteroidIndex, 1);
            this.score += 10;
          }
        });

        this.enemies.forEach((enemy, enemyIndex) => {
          if (this.isColliding(bullet, enemy)) {
            this.bullets.splice(bulletIndex, 1);
            this.enemies.splice(enemyIndex, 1);
            this.score += 20;
          }
        });
      });

      this.asteroids.forEach((asteroid, index) => {
        if (
          this.isColliding(asteroid, {
            x: this.spaceshipX,
            y: this.$el.clientHeight - 70,
            width: 50,
            height: 50,
          })
        ) {
          this.asteroids.splice(index, 1);
          this.lives -= 1;
          if (this.lives <= 0) this.gameOver();
        }
      });

      this.enemies.forEach((enemy, index) => {
        if (
          this.isColliding(enemy, {
            x: this.spaceshipX,
            y: this.$el.clientHeight - 70,
            width: 50,
            height: 50,
          })
        ) {
          this.enemies.splice(index, 1);
          this.lives -= 1;
          if (this.lives <= 0) this.gameOver();
        }
      });
    },
    isColliding(a, b) {
      return (
        a.x < b.x + 50 && a.x + 10 > b.x && a.y < b.y + 50 && a.y + 20 > b.y
      );
    },
    gameOver() {
      this.stopGame();
      this.showModal = true;
    },
    resetGame() {
      this.spaceshipX = 200;
      this.bullets = [];
      this.asteroids = [];
      this.enemies = [];
      this.score = 0;
      this.lives = 3;
      this.startGame();
      this.$nextTick(() => {
        this.$refs.gameContainer.focus();
      });
    },
    handleRestart() {
      this.resetGame();
      this.showModal = false;
    },
  },
};
</script>

<style scoped>
.game-container {
  position: relative;
  width: 100%;
  height: 98vh;
  background-image: url("../assets/space.png");
  overflow: hidden;
}
.score,
.lives {
  position: absolute;
  color: white;
  font-size: 20px;
}
.game-title {
  color: white;
  font-size: 26px;
  margin-top: 10px;
}
.score {
  top: 10px;
  left: 10px;
}
.lives {
  top: 10px;
  right: 10px;
}
</style>
