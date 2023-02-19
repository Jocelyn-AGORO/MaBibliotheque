<template>
  <div class="modal-wrapper">
    <div class="modal">
      <div class="close">
        <IconClose @click="$emit('closemedium')" />
      </div>
      <AddBookForm />
    </div>
  </div>
</template>

<script setup>
import AddBookForm from "./AddBookForm.vue"
import IconClose from "./icons/IconClose.vue";
</script>

<style scoped>
.modal-wrapper{
  position: fixed;
  top:0;
  left:0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  max-height: 100vh;
  overflow-y: auto;
  overflow-x: hidden;
  padding-top: 80px;
  padding-bottom: 80px;
  z-index: 100;
}
.modal{
  --width: 325px;
  --height: 350px;
  position: relative;
  width: var(--width);
  height: var(--height);
  border-radius: 15px;
  background-color: #424242;
  color: #868686;
  z-index: 100;
}
.close{
  display: flex;
  justify-content: flex-end;
  margin: 10px;
}
</style>