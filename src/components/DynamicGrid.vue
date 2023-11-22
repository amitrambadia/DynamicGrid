import Dropdown from 'primevue/dropdown';

<template>
  <div>
    <h2>Dynamic Grid Demo</h2>
    <select v-model="selected" class="form-control sl">
      <option v-for="option in data.options" v-bind:key="option.id">
        {{ option.value }}
      </option>
    </select>

    <button @click="addBox">Add Box</button>
    <button @click="resetGrid">Reset</button>
  </div>
</template>

<script>
import { ref } from "vue";

export default {
  name: "App",
  setup() {
    const columnOptions = [1, 2, 3, 4, 5];
    const selectedColumns = ref(columnOptions[0]);
    const boxes = ref([]);

    const addBox = () => {
      const index = boxes.value.length + 1;
      const box = { id: index, index };
      boxes.value.push(box);
    };

    const removeBox = (boxId) => {
      boxes.value = boxes.value.filter((box) => box.id !== boxId);
    };

    const resetGrid = () => {
      boxes.value = [];
      selectedColumns.value = columnOptions[0];
    };

    return {
      columnOptions,
      selectedColumns,
      boxes,
      addBox,
      removeBox,
      resetGrid,
    };
  },
};
</script>

<style scoped>
.grid-container {
  display: grid;
  grid-template-columns: repeat(var(--columns), 1fr);
  gap: 10px;
}

.box {
  border: 1px solid #ccc;
  padding: 10px;
  text-align: center;
}

select {
  margin-right: 10px;
}
</style>
