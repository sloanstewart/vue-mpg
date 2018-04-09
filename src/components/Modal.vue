<template>
      <div class="list-item-modal">
      <span>{{id}}</span>
        <a class="btn-text" @click.prevent="handleRemove(id)" href=""> Remove </a>
        <a class="btn-text" @click="handleCancel" href=""> Cancel </a>
      </div>
</template>


<script>
export default {
  name: "Modal",
  props: ["id"],
  methods: {
    handleCancel(e) {
      e.preventDefault();
      console.log("Cancel");
    },
    handleRemove(id) {
      console.log("remove", id);
      this.$emit("remove", id);
    }
  }
};
</script>

<style scoped>
.list-item-modal {
  display: flex;
  justify-content: space-around;
  background: rgba(255, 255, 255, 1);
  /* position: absolute;
  top: 50%;
  left: 50%; */
  padding: 1rem;
  /* transform: translate(-50%, -50%); */
  z-index: 999;
  /* border: 1px solid; */
  border-radius: 3px;
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
  animation: slide-down 0.2s ease;
}
.btn-text {
  text-transform: uppercase;
  text-decoration: none;
}
@keyframes slide-down {
  0% {
    opacity: 0;
    transform: translateY(-2rem);
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
