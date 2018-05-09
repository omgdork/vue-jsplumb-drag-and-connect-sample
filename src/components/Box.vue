<template>
  <div class="box">
    <textarea class="txt-box" rows="3"></textarea>
    <button class="btn-connect" @mousedown="startConnect">+</button>
  </div>
</template>

<script>
export default {
  name: 'DraggableBox',
  props: {
    startConnect: {
      type: Function,
      required: true
    },
    detachConnection: {
      type: Function,
      required: false
    }
  },
  mounted () {
    const box = jsPlumb.draggable(this.$attrs.id, {
      containment: true
    })

    box.bind('connectionDetached', this.$props.detachConnection)
  }
}
</script>

<style scoped>
.box {
  background: #fff;
  border: 1px solid #eee;
  border-radius: 10px;
  cursor: move;
  left: 10px;
  position: absolute;
  padding: 20px;
  top: 10px;
  z-index: 1;
}

.txt-box {
  border: 0;
  cursor: default;
  font: normal 16px/1.8 sans-serif;
  resize: none;
  width: 100%;
}

.btn-connect {
  background: #007fff;
  border: 0;
  color: #fff;
  cursor: grabbing;
  height: 30px;
  position: absolute;
  right: -30px;
  top: calc(50% - 15px);
  width: 30px;
}
</style>
