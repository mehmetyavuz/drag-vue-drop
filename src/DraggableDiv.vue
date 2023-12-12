<template>
  <div ref="draggableContainer"
       :class="[{dragging}, 'draggable-container']"
       @mousedown="dragMouseDown">
    <slot />
  </div>
</template>

<script>

export default {
  name: 'DraggableDiv',
  props: ['order'],
  data: function () {
    return {
      dragging: false,
      positions: {
        clientX: undefined,
        clientY: undefined,
      }
    }
  },
  methods: {
    dragMouseDown: function (event) {
      event.preventDefault()
      // get the mouse cursor position at startup:
      this.positions.clientX = event.clientX
      this.positions.clientY = event.clientY

      this.$refs.draggableContainer.style.top = this.$refs.draggableContainer.offsetTop + 'px'
      this.$refs.draggableContainer.style.left = this.$refs.draggableContainer.offsetLeft + 'px'

      document.onmousemove = this.handleDrag
      document.onmouseup = this.handleDrop
    },
    handleDrag: function (event) {
      event.preventDefault()
      this.dragging = true
      const movementX = this.positions.clientX - event.clientX
      const movementY = this.positions.clientY - event.clientY
      this.positions.clientX = event.clientX
      this.positions.clientY = event.clientY

      // set the element's new position:
      const divStyle = window.getComputedStyle(this.$refs.draggableContainer);

      const top = (Number.parseInt(divStyle.top) - movementY)
      const left = (Number.parseInt(divStyle.left) - movementX)
      this.$refs.draggableContainer.style.top = top + 'px'
      this.$refs.draggableContainer.style.left = left + 'px'


      this.$emit('dragging', [left, top])
    },
    handleDrop() {
      this.$emit('dropped', this.order)
      this.dragging = false
      document.onmouseup = null
      document.onmousemove = null
    }
  }
}
</script>