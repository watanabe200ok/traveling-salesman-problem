/**
 * 都市
 */
<template>
  <rect :x="this.rectX()" :y="this.rectY()" :width="5" :height="5" fill="#ff0000" />
</template>

<script>
export default {
  name: 'City',
  props: {
    // 座標
    x: { type: Number, default: 0 },
    y: { type: Number, default: 0 }
  },
  data () {
    return {}
  },
  methods: {
    rectX () {
      return this.x - 3
    },
    rectY () {
      return this.y - 3
    }
  }
}
</script>
