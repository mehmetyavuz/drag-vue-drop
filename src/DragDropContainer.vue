<script>
import DraggableDiv from './DraggableDiv.vue'
import './style.css'

export default {
  components: {
    DraggableDiv
  },
  emits: ['dropped'],
  props: {
    gap: {
      type: Number,
      default: 10
    },
    list: {
      type: Array,
      default: []
    }
  },
  data() {
    return {
      draggingOrder: -1,
      isMovingDirectionRight: false
    }
  },
  mounted() {
    const draggableRect = document.querySelector('.block-draggable').getBoundingClientRect();
    this.$refs.shadowDiv.style.width = `${draggableRect.width}px`;
    this.$refs.shadowDiv.style.height = `${draggableRect.height}px`;
  },
  methods: {
    handleDrag([x, y]) {
      this.$refs.shadowDiv.classList.remove('hidden')
      let newOrder = this.getShadowOrder(x, y)
      if (newOrder === undefined) return

      this.isMovingDirectionRight = this.draggingOrder <= newOrder;
      newOrder = this.isMovingDirectionRight ? newOrder + 1 : newOrder;
      this.draggingOrder = newOrder;

      const list = document.querySelectorAll('.block:not(.dragging)');
      newOrder === list.length
          ? list[0].parentElement.append(this.$refs.shadowDiv)
          : list[newOrder]?.before(this.$refs.shadowDiv)
    },
    handleDrop(order) {

      this.$refs.shadowDiv.classList.add('hidden')

      const newOrder = this.isMovingDirectionRight
          ? this.draggingOrder - 1
          : this.draggingOrder;
      const item = this.list[order]
      this.list.splice(order, 1)
      this.list.splice(newOrder, 0, item)

      this.$emit('dropped', newOrder)
    },
    getShadowOrder(movingElLeft, movingElTop) {
      const items = document.querySelectorAll('.block:not(.dragging)')
      const height = items[0]?.offsetHeight;
      const width = items[0]?.offsetWidth;
      const halfWidth = width / 2;
      const movingMidX = movingElLeft + halfWidth;
      const movingMidY = movingElTop + height / 2;

      const shadowRect = this.$refs.shadowDiv.getBoundingClientRect();
      if (shadowRect.left - this.gap <= movingMidX && movingMidX <= shadowRect.right + this.gap
          && shadowRect.top - this.gap <= movingMidY && movingMidY <= shadowRect.bottom + this.gap) {
        return;
      }

      for (let [index, item] of items.entries()) {
        const itemRect = item.getBoundingClientRect();
        if (itemRect.left - this.gap <= movingMidX && movingMidX <= itemRect.right + this.gap && movingElTop <= itemRect.top + height / 2) {
          return index;
        }
      }
      return items.length - 1;
    }
  }
}
</script>

<template>
  <div class="container" :style="`grid-gap:${gap}px`">
    <div ref="shadowDiv" class="block hidden">
      <slot name="shadowBox"/>
    </div>
    <template v-for="(block, index) in list" :key="'block'+index">
      <DraggableDiv
          :bg="block" :order="index"
          @dropped="handleDrop"
          @dragging="handleDrag"
          class="block block-draggable">
        <slot :item="block" :index="index"/>
      </DraggableDiv>
    </template>
  </div>
</template>