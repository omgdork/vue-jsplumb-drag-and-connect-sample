<template>
  <div class="wrapper" @mouseup="endConnect">
    <button id="btn-add" @click="addBox">Add Box</button>
    <section id="sec-drag">
      <draggable-box
        v-for="box in boxes"
        v-bind:key="box.id"
        v-bind:id="box.id"
        :startConnect="startConnect"
        :detachConnection="detachConnection"
      ></draggable-box>
    </section>
  </div>
</template>

<script>
import DraggableBox from './Box'

export default {
  name: 'Drag',
  data () {
    return {
      title: 'Drag and Connect',
      boxes: [],
      latestBoxId: 0,
      isConnecting: false,
      connectSource: null
    }
  },
  components: {
    'draggable-box': DraggableBox
  },
  methods: {
    addBox () {
      this.boxes.push({
        id: `box-${++this.latestBoxId}`,
        text: '',
        connections: []
      })
    },
    startConnect (e) {
      const btnConnect = e.target
      const box = btnConnect.closest('.box')

      this.isConnecting = true
      this.connectSource = box.id
    },
    endConnect (e) {
      if (this.isConnecting && (e.target.classList.contains('.box') || e.target.closest('.box'))) {
        const box = e.target.classList.contains('.box') ? e.target : e.target.closest('.box')

        jsPlumb.connect({
          source: this.connectSource,
          target: box.id,
          anchor: ['Right', 'Left'],
          endpoint: 'Dot'
        })

        jsPlumb.repaintEverything()

        this.boxes.find(({ id }) => id === this.connectSource).connections.push(box.id)
        this.boxes.find(({ id }) => id === box.id).connections.push(this.connectSource)

        this.isConnecting = false
        this.connectSource = null

        console.log('connected', this.boxes)
      }
    },
    detachConnection (e) {
      const { sourceId, targetId } = e
      const source = this.boxes.find(({ id }) => id === sourceId)
      const target = this.boxes.find(({ id }) => id === targetId)

      source.connections = source.connections.filter((id) => id !== targetId)
      target.connections = target.connections.filter((id) => id !== sourceId)

      console.log('disconnected', this.boxes)
    }
  },
  mounted () {
    let jsPlumbScript = document.createElement('script')

    jsPlumbScript.src = 'https://jsplumbtoolkit.com/lib/jsplumb.min.js'
    jsPlumbScript.onload = () => {
      jsPlumb.ready(() => {
        console.log('jsPlumb ready.')
      })
    }
    document.head.appendChild(jsPlumbScript)
  }
}
</script>

<style>
* {
  margin: 0;
  outline: none;
  padding: 0;
}

html, body {
  font: normal 14px/1.8 sans-serif;
}

.wrapper {
  padding: 20px 10px;
}

#btn-add {
  background: #007fff;
  border: 0;
  border-radius: 10px;
  color: #fff;
  padding: 20px;
  text-transform: uppercase;
}

#sec-drag {
  background: #f2f2f2;
  height: 500px;
  margin: 10px 0;
  position: relative;
  width: 100%;
}
</style>
