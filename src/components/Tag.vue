<template>
  <div class="tag">
    <div class="tag-header">
      <div class="tag-steps">
        <div class="tag-steps-title">Ходов</div>
        <div class="tag-steps-value">{{ steps }}</div>
      </div>
      <div class="tag-time">
        <div class="tag-time-title">Время</div>
        <div class="tag-time-value">{{ currentTime }}</div>
      </div>
    </div>
    <div class="tag-win" v-if="win">
      <p>Победа!</p>
      <button class="tag-win-btn" @click="newGame">Новая игра</button>
    </div>
    <div class="tag-field" v-else>
      <div
        v-for="(item, index) in tagItems"
        class="tag-item"
        :style="{left: (10 + 64 * item.x) + 'px', top: (10 + 64 * item.y) + 'px'}"
        :key="index"
        @click="moveItem(index)"
      >
        {{ index + 1 }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tagItems: [
        {x: 0, y: 0, pos: 1},
        {x: 1, y: 0, pos: 2},
        {x: 2, y: 0, pos: 3},
        {x: 3, y: 0, pos: 4},
        {x: 0, y: 1, pos: 5},
        {x: 1, y: 1, pos: 6},
        {x: 2, y: 1, pos: 7},
        {x: 3, y: 1, pos: 8},
        {x: 0, y: 2, pos: 9},
        {x: 1, y: 2, pos: 10},
        {x: 2, y: 2, pos: 11},
        {x: 3, y: 2, pos: 12},
        {x: 0, y: 3, pos: 13},
        {x: 1, y: 3, pos: 14},
        {x: 2, y: 3, pos: 15},
      ],
      freeCoord: {x: 3, y:3},
      startTime: null,
      steps: 0,
      currentTime: '00:00',
      win: false,
      timer: null
    }
  },
  methods: {
    newGame() {
      this.startTime = new Date();
      this.steps = 0;
      this.currentTime = '00:00';
      this.win = false;
      this.timer = setInterval(this.updateTime, 1000);

      // Перемешиваем плитки
      for (let i = this.tagItems.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1));
        [this.tagItems[i], this.tagItems[j]] = [this.tagItems[j], this.tagItems[i]];
      }

      // Проверяем стартовую раскладку на решаемость
      let countPos = 0;
      for(let i = 14; i > 0; i--) {
        for(let j = i - 1; j >= 0; j-- ) {
          if(this.tagItems[i].pos < this.tagItems[j].pos) {
            countPos++;
          }
        }
      }
      if(countPos % 2) {
        this.newGame();
      }
    },
    moveItem(index) {
      let itemX = this.tagItems[index].x;
      let itemY = this.tagItems[index].y;
      let freeX = this.freeCoord.x;
      let freeY = this.freeCoord.y;

      if(itemX == freeX && (itemY + 1 === freeY || itemY - 1 === freeY)) {
        this.freeCoord.y = itemY;
        this.tagItems[index].y = freeY;
        this.steps++;
      }
      if(itemY == freeY && (itemX + 1 === freeX || itemX - 1 === freeX)) {
        this.freeCoord.x = itemX;
        this.tagItems[index].x = freeX;
        this.steps++;
      }

      // Проверка правильного решения
      if(this.freeCoord.x == 3 && this.freeCoord.y == 3) {
        let itemIdx = 0;

        for(let i = 0; i <= 3; i++) {
          for(let j = 0; j <= 3;  j++) {
            if(itemIdx < 15) {
              if(this.tagItems[itemIdx].y == i && this.tagItems[itemIdx].x == j) {
                itemIdx++;
              } else {
                return;
              }
            } else {
              this.win = true;
            }
          }
        }
      }
    },
    updateTime() {
      if(!this.win) {
        let time = Math.floor((new Date() - this.startTime) / 1000);
        let m = Math.floor(time / 60);
        let s = time % 60;
        this.currentTime = ((m < 10) ? '0' + m : m) + ':' + ((s < 10) ? '0' + s : s);
      } else {
        clearInterval(this.timer);
      }
    }
  },
  created: function() {
    this.newGame();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  $winter-frost: #e4dfcf;
  $milk-glass: #faf8f0;
  $thimbleberry: #e44652;
  $marine-blue: #043353;
  $text-color: #847f59;

  .tag {
    width: 276px;
    background-color: $winter-frost;
    padding: 5px;
    margin: 0 auto;
    color: $text-color;
  }
  .tag-header {
    display: flex;
    text-align: center;
    justify-content: space-between;
    padding: 15px;
  }
  .tag-steps, .tag-time {
    background-color: $milk-glass;
    border-radius: 5px;
    padding: 5px 10px;
  }
  .tag-steps-title, .tag-time-title {
    font-weight: bold;
  }
  .tag-win {
    height: 256px;
    padding: 10px;
    text-align: center;
    font-size: 30px;
    background-color: $milk-glass;
    border-radius: 5px;
  }
  .tag-win-btn {
    width: 100%;
    color: $milk-glass;
    font-size: 20px;
    line-height: 60px;
    background-color: $thimbleberry;
    border: 0;
    border-radius: 5px;
    padding: 0;
    cursor: pointer;
  }
  .tag-field {
    height: 256px;
    padding: 10px;
    display: flex;
    flex-wrap: wrap;
    background-color: $milk-glass;
    border-radius: 5px;
    position: relative;
  }
  .tag-item {
    width: 60px;
    height: 60px;
    background-color: $thimbleberry;
    color: $milk-glass;
    text-align: center;
    line-height: 60px;
    font-size: 28px;
    cursor: pointer;
    border-radius: 5px;
    margin: 2px;
    position: absolute;
    transition: all .5s ease;
    user-select: none;
  }
</style>
