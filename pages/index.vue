<template>
  <div class="pure-g">
    <div
      v-for="(item, index) in 4"
      class="pure-u-1 pure-u-md-1-2 pure-u-lg-1-4"
      :key="item"
    >
      <div class="container">
        <form class="pure-form">
          <legend>Test {{ item }}</legend>
          <input
            v-model="values[index]"
            @input="setColor(index)"
            type="text"
            placeholder="Color HEX"
          />
        </form>
        <div class="pallet">
          <div
            v-for="(item, counter) in 6"
            :style="`background: ${
              colorValues[index] ? colorValues[index][counter] : ''
            }`"
            class="item"
            :key="item"
          >
            {{
              colorValues[index] ? HSLToHex(colorValues[index][counter]) : ''
            }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  name: 'index',
  data() {
    return {
      values: [],
      colorValues: [],
    }
  },
  methods: {
    setColor(index) {
      const color = this.values[index].replace('#', '')
      if (color.length === 3 || color.length === 6) {
        const colorHSL = this.hexToHSL(`#${color}`)
        this.colorValues[index] = []
        this.colorValues[index].push(
          'hsl(' +
            colorHSL.h +
            ',' +
            (colorHSL.s - 12) +
            '%,' +
            (colorHSL.l - 12) +
            '%)'
        )
        this.colorValues[index].push(
          'hsl(' + colorHSL.h + ',' + colorHSL.s + '%,' + colorHSL.l + '%)'
        )
        let setL = colorHSL.l
        for (let i = 0; i < 4; i++) {
          setL = setL + 8
          this.colorValues[index].push(
            'hsl(' + colorHSL.h + ',' + colorHSL.s + '%,' + setL + '%)'
          )
        }
      }
    },
    hexToHSL(H) {
      // Convert hex to RGB first
      let r = 0,
        g = 0,
        b = 0
      if (H.length == 4) {
        r = '0x' + H[1] + H[1]
        g = '0x' + H[2] + H[2]
        b = '0x' + H[3] + H[3]
      } else if (H.length == 7) {
        r = '0x' + H[1] + H[2]
        g = '0x' + H[3] + H[4]
        b = '0x' + H[5] + H[6]
      }
      // Then to HSL
      r /= 255
      g /= 255
      b /= 255
      let cmin = Math.min(r, g, b),
        cmax = Math.max(r, g, b),
        delta = cmax - cmin,
        h = 0,
        s = 0,
        l = 0

      if (delta == 0) h = 0
      else if (cmax == r) h = ((g - b) / delta) % 6
      else if (cmax == g) h = (b - r) / delta + 2
      else h = (r - g) / delta + 4

      h = Math.round(h * 60)

      if (h < 0) h += 360

      l = (cmax + cmin) / 2
      s = delta == 0 ? 0 : delta / (1 - Math.abs(2 * l - 1))
      s = +(s * 100).toFixed(1)
      l = +(l * 100).toFixed(1)

      return { h, s, l }
    },
    HSLToHex(hsl) {
      let sep = hsl.indexOf(",") > -1 ? "," : " ";
      hsl = hsl.substr(4).split(")")[0].split(sep);

      console.log(hsl)

      let h = parseInt(hsl[0]),
        s = parseInt(hsl[1].substr(0,hsl[1].length - 1)),
        l = parseInt(hsl[2].substr(0,hsl[2].length - 1))
      console.log(h,s,l)
      s /= 100;
      l /= 100;

      let c = (1 - Math.abs(2 * l - 1)) * s,
        x = c * (1 - Math.abs(((h / 60) % 2) - 1)),
        m = l - c / 2,
        r = 0,
        g = 0,
        b = 0

      if (0 <= h && h < 60) {
        r = c
        g = x
        b = 0
      } else if (60 <= h && h < 120) {
        r = x
        g = c
        b = 0
      } else if (120 <= h && h < 180) {
        r = 0
        g = c
        b = x
      } else if (180 <= h && h < 240) {
        r = 0
        g = x
        b = c
      } else if (240 <= h && h < 300) {
        r = x
        g = 0
        b = c
      } else if (300 <= h && h < 360) {
        r = c
        g = 0
        b = x
      }
      // Having obtained RGB, convert channels to hex
      r = Math.round((r + m) * 255).toString(16)
      g = Math.round((g + m) * 255).toString(16)
      b = Math.round((b + m) * 255).toString(16)

      // Prepend 0s, if necessary
      if (r.length == 1) r = '0' + r
      if (g.length == 1) g = '0' + g
      if (b.length == 1) b = '0' + b

      return '#' + r + g + b
    },
  },
}
</script>

<style lang="sass">
.container
  display: flex
  flex-direction: column
  align-items: center
  padding: 20px
.pallet
  margin-top: 20px
  max-width: 300px
  width: calc(100% - 40px)
  border: 1px solid black
  .item
    display: flex
    align-items: center
    justify-content: center
    height: 50px
    &:last-child
      border-bottom: none
</style>
