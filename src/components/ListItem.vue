<template>
  <div class="container">
    <li :class="[listitem, isSelected && selected]" @click="select()">
      <span>{{item.date}} </span>
      <span>{{item.mileage}} </span>
      <span>{{item.gallons}} </span>
      <span>{{item.price}} </span>
      <span>{{item.mpg}} </span>
      <span :class="[removeitem, !isSelected && hidden]" @click="remove(item.id)">REMOVE</span>
    </li>
  </div>
</template>


<script>
export default {
  name: "ListItem",
  props: {
    item: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      listitem: "list-item",
      selected: "selected",
      removeitem: "remove-item",
      hidden: "hidden",
      isSelected: false
    };
  },
  methods: {
    select() {
      this.isSelected = !this.isSelected;
    },
    remove(id) {
      this.$emit("remove", id);
    }
  }
};
</script>

<style scoped>
.list-item {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
  justify-items: center;
  transition: all 200ms ease-in-out;
  background: inherit;
  animation: slide-down 250ms ease-in-out;
  border-top: none;
  border-right: none;
  border-bottom: 1px solid #eee;
  border-left: none;
  cursor: pointer;
}
.list-item:nth-last-child(2) {
  border: none;
}
.list-item > span {
  padding: 1rem 0.5rem;
}
.selected {
  background: #e8f5e9;
}
.remove-item {
  cursor: pointer;
  font-weight: bold;
  grid-column: span 5;
}
.hidden {
  visibility: hidden;
  display: none;
}
@keyframes slide-down {
  0% {
    opacity: 0;
    transform: translateY(-4rem);
  }
  50% {
    opacity: 0;
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
