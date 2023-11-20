<template>
  <div>
    <label for="columns">Select number of columns:</label>
    <select v-model="selectedColumns" @change="updateGrid">
      <option v-for="option in columnOptions" :key="option" :value="option">
        {{ option }}
      </option>
    </select>

    <button @click="addBox">Add Box</button>
    <button @click="resetGrid">Reset</button>

    <div class="grid-container">
      <div v-for="row in grid" :key="row.id" class="grid-row">
        <div v-for="box in row.boxes" :key="box.id" class="box">
          <span>{{ box.index }}</span>
          <button @click="removeBox(row.id, box.id)">X</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";

export default {
  name: "App",
  setup() {
    const columnOptions = [1, 2, 3, 4, 5];
    const selectedColumns = ref(columnOptions[0]);
    const grid = ref([]);

    const addBox = () => {
      let lastRow = grid.value[grid.value.length - 1];

      if (!lastRow || lastRow.boxes.length === selectedColumns.value) {
        lastRow = { id: grid.value.length, boxes: [] };
        grid.value.push(lastRow);
      }

      const index =
        grid.value.reduce((acc, row) => acc + row.boxes.length, 0) + 1;
      lastRow.boxes.push({ id: index, index });
    };

    const removeBox = (rowId, boxId) => {
      const row = grid.value.find((r) => r.id === rowId);
      if (row) {
        row.boxes = row.boxes.filter((box) => box.id !== boxId);
      }

      // Rearrange box positions within the same row
      row.boxes.forEach((box, index) => {
        box.index = index + 1;
      });

      // Move boxes up in other rows to fill empty spaces
      for (let i = 0; i < grid.value.length - 1; i++) {
        const currentRow = grid.value[i];
        const nextRow = grid.value[i + 1];

        if (!currentRow || !nextRow) {
          break;
        }

        const lastBoxIndex = currentRow.boxes.length;

        // Move the last box from the next row to the current row
        if (lastBoxIndex < selectedColumns.value && nextRow.boxes.length > 0) {
          const movedBox = nextRow.boxes.pop();
          movedBox.index = lastBoxIndex + 1;
          currentRow.boxes.push(movedBox);
        }
      }

      // Rearrange box positions across all rows
      let currentIndex = 1;
      for (const r of grid.value) {
        for (const box of r.boxes) {
          box.index = currentIndex++;
        }
      }

      // Trigger a re-render by creating a new array
      grid.value = [...grid.value];
    };

    const resetGrid = () => {
      grid.value = [];
      selectedColumns.value = columnOptions[0];
    };

    const updateGrid = () => {
      grid.value = [];
    };

    return {
      columnOptions,
      selectedColumns,
      grid,
      addBox,
      removeBox,
      resetGrid,
      updateGrid,
    };
  },
};
</script>

<style scoped>
.grid-container {
  display: grid;
  gap: 10px;
}

.grid-row {
  display: flex;
  flex-wrap: wrap;
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
