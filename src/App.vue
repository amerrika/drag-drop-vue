<template>
  <div class="drag-drop">
    <div class="drag-drop__header">
      <h1>
        Single List Drag & Drop with
        <span style="color: var(--green)"
          >Vue<span style="color: #35495e">.</span>js</span
        >
      </h1>
    </div>

    <ul
      ref="ul"
      class="drag-drop__list"
      @drop.prevent="onDrop"
      @dragover.prevent="onDragover"
    >
      <li
        v-for="(item, index) in items"
        :key="index"
        class="drag-drop__item"
        draggable="true"
        @dragstart="onDragstart($event, index)"
        @dragend="onDragend"
      >
        {{ item.title }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [
        {
          id: 0,
          title: "List Item A",
        },
        {
          id: 1,
          title: "List Item B",
        },
        {
          id: 2,
          title: "List Item C",
        },
        {
          id: 3,
          title: "List Item D",
        },
        {
          id: 4,
          title: "List Item E",
        },
      ],
    };
  },
  methods: {
    onDragstart(event, index) {
      event.dataTransfer.effectAllowed = "move";
      // add css class
      event.currentTarget.classList.add("dragging");
      // clear the drag data cache
      event.dataTransfer.clearData();
      // set the drag's format and data
      // use item index for the data
      event.dataTransfer.setData("text/plain", index);
    },
    onDragend(event) {
      // remove css class
      event.target.classList.remove("dragging");
    },
    onDragover(event) {
      event.dataTransfer.dropEffect = "move";
    },
    onDrop(event) {
      // html ul is needed for getting index of the drop element
      const ulItems = Array.from(this.$refs.ul.children);
      // get index of drag and drop elements
      const dragElIndex = Number(event.dataTransfer.getData("text/plain"));
      const dropElIndex = ulItems.indexOf(event.target);

      const list = this.items;
      const listUnmodified = [...list];

      // use x and y for convenience
      const x = dragElIndex;
      const y = dropElIndex;

      // a space between items
      let diff = x - y;

      if (y === null) return;

      if (diff === 1 || diff === -1) {
        // swap positions
        [list[y], list[x]] = [list[x], list[y]];
      } else {
        // 1. swap places of drag item and drop item
        list[y] = listUnmodified[x];
        // 2. sort other items
        // if dragging item up
        if (diff > 1) {
          for (let i = 1; i <= diff; i++) {
            list[y + i] = listUnmodified[y + i - 1];
          }
          // if dragging item down
        } else if (diff < -1) {
          for (let i = 0; i < Math.abs(diff); i++) {
            list[x + i] = listUnmodified[x + i + 1];
          }
        }
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100%;
  display: flex;
  justify-content: center;
}

.drag-drop {
  width: min(900px, 100% - 10px);
  display: flex;
  flex-direction: column;
  gap: 40px;
}

.drag-drop__list {
  width: 100%;
  padding: 20px;
  min-height: 10px;
}

.drag-drop__item {
  user-select: none;
  background-color: var(--green);
  color: var(--dark);
  font-weight: 500;
  padding: 10px;
  margin-bottom: 20px;
  border-radius: 5px;
  border: 1px solid var(--green);
}

.drag-drop__item:last-of-type {
  margin-bottom: 0;
}

.dragging {
  opacity: 0.5;
  border-color: white;
}

h1 {
  color: #fff;
  font-weight: 500;
  text-decoration: underline solid var(--yellow);
  text-underline-offset: 15px;
  text-decoration-thickness: 10px;
  line-height: 2;

  span {
    font-weight: 700;
  }
}
</style>
