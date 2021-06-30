<template>
  <div :style="{width: `${width}px`, height: `${height}px`}">
    <h1>Traveling Salesman Problem</h1>
    <svg xmlns="http://www.w3.org/2000/svg"
      @click="addCity($event.offsetX, $event.offsetY)">
      <city v-for="city in this.cities"
        :key="`city-${city.id}`"
        :x="city.x"
        :y="city.y" />
      <kohonen-map
        ref="kohonen"
        :width="this.width"
        :height="this.height"
        :cities="cities" />
    </svg>
  </div>
</template>

<style lang="scss">
div {
  margin: 0 auto;
}
svg {
  background: #cccccc;
  width: 100%;
  height: 100%;
}
</style>

<script>
import City from '@/components/City.vue'
import KohonenMap from './components/KohonenMap.vue'
// キャンバスのサイズ = ウインドウサイズ * この定数
const CANVAS_SIZE_RATE = 0.8
// 1秒当たりのフレーム数
const FRAMES_PER_SECOND = 20
// FRAMES_PER_SECONDによって定まるインバール(ミリ秒)
const INTERVAL_MILLISECOND = 1000 / FRAMES_PER_SECOND
// 配置できる都市の最大数
const MAX_CITY_COUNT = 256

export default {
  name: 'App',
  components: {
    City,
    KohonenMap
  },
  data () {
    const [width, height] = this.canvasSize()
    return {
      frameTimer: 0,
      width: width,
      height: height,
      cities: []
    }
  },
  methods: {
    /**
     * @returns {Number[2]} キャンバスサイズ, [幅, 高さ]
     */
    canvasSize () {
      return [
        ~~(window.innerWidth * CANVAS_SIZE_RATE),
        ~~(window.innerHeight * CANVAS_SIZE_RATE)
      ]
    },
    /**
     * ウインドウのサイズが変更された時のハンドリング
     */
    handleResize () {
      const [width, height] = this.canvasSize()
      this.cities = []
      this.width = width
      this.height = height
    },
    /**
     * 引数で指定された位置に都市を追加する。
     *
     * @param {Number} x X座標
     * @param {Number} y Y座標
     */
    addCity (x, y) {
      if (this.cities.length >= MAX_CITY_COUNT) {
        return
      }
      const city = {
        id: this.cities.length,
        x: x,
        y: y
      }
      this.cities.push(city)
      this.$refs.kohonen.reset(this.cities.length)
    }
  },
  mounted () {
    this.frameTimer = setInterval(this.$refs.kohonen.lean, INTERVAL_MILLISECOND)
    window.addEventListener('resize', this.handleResize)
  },
  beforeUnmount () {
    window.clearInterval(this.frameTimer)
    this.cities = null
  }
}
</script>
