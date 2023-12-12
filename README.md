# drag-vue-drop

This package provides a simple drag & drop feature for sorting boxes in your Vue project without any dependency.

## Package Setup

### Installation
```sh
npm i drag-vue-drop
```

### Implementation

```javascript
import {ref} from "vue";
import {DragDropContainer} from "drag-vue-drop";

const blocks = ref(['pink', 'gray', 'aqua', 'lightgreen', 'orange'])

const handleDrop = (index) => {
  console.log('dropped, new index:', index);
}
```
```html
<template>
  <main>
    <DragDropContainer :gap="20" :list="blocks" @dropped="handleDrop">
      <template #shadowBox>
        <div style="border: #2c3e50 2px dashed; border-radius: 20px; height: 100px"></div>
      </template>
      <template #default="{item, index}">
        <div class="box" :style="`background-color:${item}`">{{ index }}</div>
      </template>
    </DragDropContainer>
  </main>
</template>
```
```css
.box {
  height: 100px;
  width: 100px;
  border-radius: 20px;
  color: #282828;
  font-weight: bold;
  font-size: x-large;
  display: flex;
  align-items: center;
  justify-content: center;
}
```
### Demo
![drag-vue-drop.gif](https://github.com/mehmetyavuz/drag-vue-drop/raw/main/drag-vue-drop.gif)