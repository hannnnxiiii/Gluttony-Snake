<script setup>
import {
  CaretTop,
  CaretLeft,
  CaretRight,
  CaretBottom,
} from "@element-plus/icons-vue"
import { ref } from "vue"

// 分数
const score = ref(0)
// 开始游戏按钮
const startButton = ref(null)
// 控制面板
const controlPanel = ref(null)
// 蛇的长度
const length = ref(5)
// 蛇
const snake = ref(null)
// 球
const ball = ref(null)
// x轴方向
const xDirection = ref(0)
// y轴方向
const yDirection = ref(10)
// 存储地图每块地皮情况
const snakeOrNot = new Map()
// 成功音效
const success = ref(null)
// 游戏状态
const isGaming = ref(0)
// 游戏介绍
const introduction = ref(null)
// 定时器
let timer = null
// 球状态切换倒计时
let ballTimer = null
// 球的状态
let plusOrNot = 0
// 遮罩层
const mask = ref(null)
// 上下左右按钮
const upButton = ref(null)
const leftButton = ref(null)
const rightButton = ref(null)
const downButton = ref(null)
// 开始游戏
const startGame = () => {
  introduction.value.style.display = "none"
  length.value = 5
  // 清空分数
  score.value = 0
  // 清空地图
  snakeOrNot.clear()
  // 去除虚化
  for (let i = length.value - 1; i >= 0; i--) {
    snake.value[i].style.filter = "blur(0px)"
  }
  ball.value.style.filter = "blur(0px)"
  // 去除遮罩
  mask.value.style.display = "none"
  // 切换按钮
  isGaming.value = 1
  // 初始化蛇头位置
  for (let i = 0; i < 5; i++) {
    snake.value[i].style.top = "240px"
    snake.value[i].style.left = "240px"
    snake.value[i].style.display = "block"
  }
  // 显示球
  createBall()
  ball.value.style.display = "block"
  // 计时处理蛇的移动
  timer = setInterval(() => {
    snakeLogic()
  }, 50)
}
// 蛇的移动逻辑
// 上下左右穿越旗帜
let crossUpFlag = -2
let crossLeftFlag = -2
let crossRightFlag = -2
let crossDownFlag = -2
const snakeLogic = () => {
  // 遍历处理每个方块

  for (let i = length.value - 1; i >= 0; i--) {
    // 头就往对应方向前进
    if (i === 0) {
      // 判断前进的位置有无身体
      if (
        snakeOrNot.get(
          `${parseInt(snake.value[i].style.top) + yDirection.value}px-${
            parseInt(snake.value[i].style.left) + xDirection.value
          }px`
        )
      ) {
        // 停止定时器
        clearInterval(timer)
        console.log("游戏结束")
        for (let i = length.value - 1; i >= 0; i--) {
          snake.value[i].style.filter = "blur(2px)"
        }
        ball.value.style.filter = "blur(2px)"
        mask.value.style.display = "flex"
        // 切换按钮
        isGaming.value = 0
      }
      // 如果要前进的位置超出了地图，那就穿过去到另一边
      if (parseInt(snake.value[i].style.top) + yDirection.value > 490) {
        snake.value[i].style.transition = "none"
        snake.value[i].style.top = "0px"
        crossUpFlag = i
      } else if (parseInt(snake.value[i].style.top) + yDirection.value < 0) {
        snake.value[i].style.transition = "none"
        snake.value[i].style.top = "490px"
        crossDownFlag = i
      } else if (parseInt(snake.value[i].style.left) + xDirection.value > 490) {
        snake.value[i].style.transition = "none"
        snake.value[i].style.left = "0px"
        crossRightFlag = i
      } else if (parseInt(snake.value[i].style.left) + xDirection.value < 0) {
        snake.value[i].style.transition = "none"
        snake.value[i].style.left = "490px"
        crossLeftFlag = i
      } else {
        snake.value[i].style.top =
          parseInt(snake.value[i].style.top) + yDirection.value + "px"
        snake.value[i].style.left =
          parseInt(snake.value[i].style.left) + xDirection.value + "px"
        snake.value[i].style.transition = "all linear 50ms"
      }
      snakeOrNot.set(
        `${snake.value[i].style.top}-${snake.value[i].style.left}`,
        1
      )
    } else {
      // 处理显示问题
      if (
        snake.value[i].style.top !== snake.value[i - 1].style.top &&
        snake.value[i].style.left !== snake.value[i - 1].style.left
      ) {
        snake.value[i].style.display = "block"
      }
      // 蛇尾清除位置
      if (i === length.value - 1) {
        snakeOrNot.set(
          `${snake.value[i].style.top}-${snake.value[i].style.left}`,
          0
        )
      }
      // 如果是身体就只要去上一个方块的位置就行
      if (crossUpFlag + 1 === i) {
        snake.value[i].style.transition = "none"
        snake.value[i].style.top = snake.value[i - 1].style.top
        snake.value[i].style.left = snake.value[i - 1].style.left
        crossUpFlag = i
      } else if (crossLeftFlag + 1 === i) {
        snake.value[i].style.transition = "none"
        snake.value[i].style.top = snake.value[i - 1].style.top
        snake.value[i].style.left = snake.value[i - 1].style.left
        crossLeftFlag = i
      } else if (crossRightFlag + 1 === i) {
        snake.value[i].style.transition = "none"
        snake.value[i].style.top = snake.value[i - 1].style.top
        snake.value[i].style.left = snake.value[i - 1].style.left
        crossRightFlag = i
      } else if (crossDownFlag + 1 === i) {
        snake.value[i].style.transition = "none"
        snake.value[i].style.top = snake.value[i - 1].style.top
        snake.value[i].style.left = snake.value[i - 1].style.left
        crossDownFlag = i
      } else {
        snake.value[i].style.top = snake.value[i - 1].style.top
        snake.value[i].style.left = snake.value[i - 1].style.left
        snake.value[i].style.transition = "all linear 50ms"
      }
    }
  }
  // 如果吃球，重新生成球，蛇长度加1
  if (
    parseInt(snake.value[0].style.top) === parseInt(ball.value.style.top) &&
    parseInt(snake.value[0].style.left) === parseInt(ball.value.style.left)
  ) {
    success.value.load()
    success.value.play()
    if (plusOrNot) {
      score.value += 500
    } else {
      score.value += 100
    }
    createBall()
    length.value += 1
  }
}
// 切换方向函数
const turnDirection = (x, y) => {
  // if (turnFlag) {
  //   return
  // }
  // // 不能直接180调头
  // if (x + xDirection.value === 0 && x) {
  //   return
  // }
  // if (y + yDirection.value === 0 && y) {
  //   return
  // }
  // xDirection.value = x
  // yDirection.value = y
  // turnFlag = 1
  // timer1 = setTimeout(() => {
  //   turnFlag = 0
  // }, 50)
  // 防止扭脖子扭死，同时减少粘滞感
  if (
    parseInt(snake.value[0].style.top) + y ===
      parseInt(snake.value[1].style.top) &&
    parseInt(snake.value[0].style.left) + x ===
      parseInt(snake.value[1].style.left)
  ) {
    return
  }
  xDirection.value = x
  yDirection.value = y
}

// 产球函数
// 时间旗帜
let twinkleTime = 0
const createBall = () => {
  plusOrNot = 1
  twinkleTime = 0
  if (ballTimer) {
    clearInterval(ballTimer)
  }
  do {
    ball.value.style.top = parseInt((Math.random() * 500) / 10) * 10 + "px"
    ball.value.style.left = parseInt((Math.random() * 500) / 10) * 10 + "px"
  } while (snakeOrNot.get(`${ball.value.style.top}-${ball.value.style.left}`))
  ballTimer = setInterval(() => {
    ball.value.style.background === "red"
      ? (ball.value.style.background = "pink")
      : (ball.value.style.background = "red")
    twinkleTime++
    if (twinkleTime === 10) {
      plusOrNot = 0
      ball.value.style.background = "red"
      clearInterval(ballTimer)
    }
  }, 500)
}

// 处理按键事件
document.addEventListener("keydown", (e) => {
  if (e.key === "Enter" && !isGaming.value) {
    startButton.value.$el.click()
  }
  if (!isGaming.value) {
    return
  }
  if (e.key === "w" || e.key === "W" || e.key === "ArrowUp") {
    upButton.value.$el.click()
  }
  if (e.key === "A" || e.key === "a" || e.key === "ArrowLeft") {
    leftButton.value.$el.click()
  }
  if (e.key === "d" || e.key === "D" || e.key === "ArrowRight") {
    rightButton.value.$el.click()
  }
  if (e.key === "s" || e.key === "S" || e.key === "ArrowDown") {
    downButton.value.$el.click()
  }
})
</script>

<template>
  <div class="flex flex-col items-center">
    <!-- 地图 -->
    <div
      class="w-[504px] h-[504px] border-2 border-black mx-auto my-[10px] relative"
    >
      <!-- 蛇 -->
      <div
        v-for="item in length"
        :key="item"
        class="w-[10px] h-[10px] bg-[#409eff] absolute rounded-[3px] hidden transition-all ease-linear duration-[50ms]"
        ref="snake"
      ></div>
      <!-- 球 -->
      <div
        class="w-[10px] h-[10px] rounded-full absolute hidden top-[-10px] left-[-10px] transition-colors duration-[500ms]"
        style="background-color: red"
        ref="ball"
      ></div>
      <!-- 遮罩 -->
      <div
        class="hidden w-[500px] h-[500px] bg-[#0000008c] z-50 absolute text-[40px] text-white font-bold flex justify-center flex-col items-center"
        ref="mask"
      >
        游戏结束
        <div>你的得分是：{{ score }}</div>
      </div>
      <!-- 游戏说明 -->
      <p
        class="flex flex-col font-bold text-[20px] text-[#409eff] justify-around h-[400px]"
        ref="introduction"
      >
        <span
          >向上:w或⭡, 向左:a或⭠, 向右:d或⭢, 向下:s或⭣, 点击网页内按钮也行</span
        >
        <span>点击开始按钮或按下Enter开始游戏</span>
        <span>球刚出现的时候会闪烁五秒</span>
        <span>吃到闪烁球加500分, 红球100分, 都是加1格长度</span>
        <span>也就是说，尽快吃球，你就能在更低的难度下达到更高分！</span>
        <span>祝你好运，现在开始吧！</span>
      </p>
    </div>
    <!-- 开始游戏 -->
    <el-button
      type="primary"
      @click="startGame()"
      ref="startButton"
      v-if="!isGaming"
      >开始游戏</el-button
    >
    <!-- 控制面板 -->
    <div class="flex justify-between w-[500px]" ref="controlPanel" v-else>
      <!-- 分数 -->
      <div>分数：{{ score }}</div>
      <!-- 控制按钮 -->
      <div class="flex flex-col items-center mr-[173px]">
        <el-button
          type="primary"
          :icon="CaretTop"
          @click="turnDirection(0, -10)"
          ref="upButton"
        ></el-button>
        <div class="flex my-[10px]">
          <el-button
            type="primary"
            :icon="CaretLeft"
            class="mr-[50px]"
            @click="turnDirection(-10, 0)"
            ref="leftButton"
          ></el-button>
          <el-button
            type="primary"
            :icon="CaretRight"
            @click="turnDirection(10, 0)"
            ref="rightButton"
          ></el-button>
        </div>
        <el-button
          type="primary"
          :icon="CaretBottom"
          @click="turnDirection(0, 10)"
          ref="downButton"
        ></el-button>
      </div>
    </div>
    <!-- 音效 -->
    <audio ref="success">
      <source src="./assets/audio/success.mp3" type="audio/ogg" />
    </audio>
  </div>
</template>
