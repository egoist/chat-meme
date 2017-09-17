<template>
  <div id="app">
    <div class="controls">
      <div class="control">
        <label for="files" class="btn">Choose avatar</label>
        <input id="files" @change="handleAvatar" style="display:none" type="file">
      </div>
      <div class="control">
        <input type="text" v-model="form.username" placeholder="username">
      </div>
      <div class="control">
        <textarea v-model="form.content" placeholder="content"></textarea>
      </div>
      <div class="control">
        <input type="text" v-model="form.time" placeholder="time">
      </div>
      <div class="control" v-if="typeof form.avatar !== 'string'">
        <button @click="download">Download</button>
      </div>
    </div>

    <div ref="canvas"></div>
  </div>
</template>

<script>
import download from 'downloadjs'

export default {
  data() {
    return {
      form: {
        username: 'EGOIST',
        content: 'iPhone X\niPhone XL\niPhone XXL',
        time: '1/24/16',
        avatar: 'https://github.com/egoist.png'
      }
    }
  },

  mounted() {
    this.draw()
  },

  watch: {
    form: {
      handler: 'draw',
      deep: true
    }
  },

  methods: {
    draw() {
      const { username, content, time, avatar } = this.form
      const { canvas } = this.$refs

      let $canvas = canvas.querySelector('#canvas')
      if ($canvas) {
        canvas.removeChild($canvas)
      }
      $canvas = document.createElement('canvas')
      $canvas.id = 'canvas'
      $canvas.height = 200
      $canvas.width = 500
      canvas.appendChild($canvas)
      const ctx = $canvas.getContext('2d')

      const offsetLeft = 60

      ctx.fillStyle = 'white'
      ctx.fillRect(0, 0, $canvas.width, $canvas.height)

      if (avatar) {
        this.renderAvatar(avatar)
      }

      ctx.font = 'bold 18px sans-serif'
      ctx.fillStyle = '#07a'
      ctx.fillText(username, offsetLeft, 20)
      const userNameWidth = ctx.measureText(username).width

      const timeOffsetLeft = userNameWidth + 5 + offsetLeft
      ctx.font = '18px sans-serif'
      ctx.fillStyle = '#858C93'
      ctx.fillText(time, timeOffsetLeft, 20)

      ctx.font = 'bold 54px Helvetica'
      ctx.fillStyle = '#000'
      content.split('\n').forEach((text, index) => {
        ctx.fillText(text, offsetLeft, (index + 1) * 55 + 20)
      })
    },

    handleAvatar(e) {
      this.form.avatar = e.target.files[0]
    },

    renderAvatar(avatar) {
      const $canvas = this.$refs.canvas.querySelector('#canvas')
      const ctx = $canvas.getContext('2d')
      const img = new Image()
      img.onload = () => {
          ctx.drawImage(img, 10, 5, 40, 40)
      }
      img.src = typeof avatar === 'string' ? avatar : URL.createObjectURL(avatar)
    },

    download() {
      const image = this.$refs.canvas.querySelector('#canvas').toDataURL()
      download(image, 'lol.png', 'image/png')
    }
  }
}
</script>

<style>
.btn {
  border: 1px solid #ccc;
  padding: 5px 10px;
  border-radius: 3px;
  font-size: 14px;
  cursor: pointer;
  background: #f9f9f9;
}

.btn:hover {
  background-color: #fff;
  border-color: #999;
}

.controls {
  margin-bottom: 10px;
}

#canvas {
  width: 250px;
  height: 100px;
}
</style>
