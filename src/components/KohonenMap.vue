<template>
  <rect v-if="showProgress" x="0" y="0" :width="(width * progress)" :height="height" fill="#88FF88" fill-opacity="0.3" />
  <line v-for="line in lines" :key="line.id"
    :x1="line.x1" :y1="line.y1" :x2="line.x2" :y2="line.y2" stroke-width="1" stroke="#0000FF" />
</template>

<script>
const CELL_COUNT_PER_CITY = 3
const MAX_TIME = 200
const DECAY_PARAM = 10.0
const BASE_TENSION = 0.5
const BASE_TENSION_RATIO = 0.9

export default {
  name: 'KohonenMap',
  props: {
    // 座標
    width: { type: Number, default: 0 },
    height: { type: Number, default: 0 },
    cities: { type: Array[Object], default: [] }
  },
  data () {
    return {
      time: 0,
      cells: []
    }
  },
  computed: {
    citySize () {
      return this.cities.length
    },
    cellSize () {
      return this.cells.length
    },
    showProgress () {
      return this.cities.length > 1
    },
    progress () {
      return this.time / MAX_TIME
    },
    lines () {
      const size = this.cells.length
      if (size <= 1) {
        return []
      }
      const list = []
      let cell1 = this.cells[0]
      for (let i = 1; i < size; i++) {
        const cell2 = this.cells[i]
        list.push(this.newline(list.length, cell1, cell2))
        cell1 = cell2
      }
      list.push(this.newline(list.length, cell1, this.cells[0]))
      return list
    }
  },
  methods: {
    reset (citySize) {
      this.time = 0
      // セルを円形状に等間隔で配置する
      const size = citySize * CELL_COUNT_PER_CITY
      const centerX = this.width / 2
      const centerY = this.height / 2
      const delta = 2 * Math.PI / size
      const radius = Math.min(centerX, centerY) / 2
      const cells = []
      for (let i = 0, angle = 0; i < size; i++, angle += delta) {
        const cell = {
          id: i,
          x: centerX + radius * Math.cos(angle),
          y: centerY + radius * Math.sin(angle)
        }
        cells.push(cell)
      }
      this.cells = cells
    },
    newline (id, cell1, cell2) {
      return {
        id: id,
        x1: cell1.x,
        y1: cell1.y,
        x2: cell2.x,
        y2: cell2.y
      }
    },
    lean () {
      if (MAX_TIME < this.time) {
        return
      }
      this.cities.forEach(city => {
        const closestCellIndex = this.closestCellIndex(city)
        this.updateCell(city, closestCellIndex)
      })
      this.time++
    },
    closestCellIndex (city) {
      let outcome
      let minDistance = Infinity
      this.cells.forEach((cell, index) => {
        const distance = this.distance(city.x, city.y, cell.x, cell.y)
        if (distance < minDistance) {
          minDistance = distance
          outcome = index
        }
      })
      return outcome
    },
    distance (x1, y1, x2, y2) {
      // 最短距離を求める目的で利用するので、最後まで計算しない
      return Math.pow(x1 - x2, 2) + Math.pow(y1 - y2, 2)
    },
    updateCell (city, closestCellIndex) {
      // decay: 時間と共に減衰していく値: 0 <= decay < 1
      const decay = this.decay(this.time)
      // range: 都市から最も近いセルを基準として、何個隣のセルまで位置調整をするか
      const range = this.range(decay)

      // 都市から最も近いセルを都市側に大きく引き寄せる
      const cell = this.cells[closestCellIndex]
      let tension = this.tension(decay, range, 0)
      cell.x += (tension * (city.x - cell.x))
      cell.y += (tension * (city.y - cell.y))

      // 上記で都市側に引き寄せたセルの近隣セルを都市側に引き寄せる
      for (let [i, left, right] = [1, closestCellIndex - 1, closestCellIndex + 1]; i <= range; i++, left--, right++) {
        tension = this.tension(decay, range, i)
        // 左側のセルを引き寄せる
        while (left < 0) {
          left += this.cellSize
        }
        const leftCell = this.cells[left]
        leftCell.x += (tension * (city.x - leftCell.x))
        leftCell.y += (tension * (city.y - leftCell.y))
        // 右側のセルを引き寄せる
        while (this.cellSize <= right) {
          right -= this.cellSize
        }
        const rightCell = this.cells[right]
        rightCell.x += (tension * (city.x - rightCell.x))
        rightCell.y += (tension * (city.y - rightCell.y))
      }
    },
    decay (time) {
      const MIN = DECAY_PARAM / (DECAY_PARAM + MAX_TIME)
      return DECAY_PARAM / (DECAY_PARAM + time) - MIN
    },
    range (decay) {
      return ~~(this.cellSize * decay)
    },
    tension (decay, range, index) {
      const tension = BASE_TENSION + BASE_TENSION_RATIO * decay
      return range > 0 ? tension * (1.0 - index / range) : range
    }
  }
}
</script>
