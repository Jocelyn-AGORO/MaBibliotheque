<template>
  <div>
    <form method="get" @submit.prevent="sendSearch">
      <p class="wrapper">
        <label class="label" for="recherche">Rechercher</label>
        <input class="search" type="text" id="recherche" name="recherche" v-model="recherche" @input="typing">
      </p>
      <div class="button-wrapper">
        <button class="search-button" type="submit">
          <IconSearch/>
          Rechercher
        </button>
      </div>
    </form>
  </div>
</template>

<script setup >
import {inject, ref} from "vue"
import IconSearch from "./icons/IconSearch.vue"

// inject the global emitter
const emitter = inject('emitter')

const recherche = ref('');

const typing = () => {
  console.log(recherche.value)
}

const sendSearch = () => {
  emitter.emit('search', recherche.value)
  console.log(recherche.value)
}

</script>

<style scoped>
.search-button{
  display: flex;
  justify-content:space-between;
  align-items:center;
  gap: 2.5px;
  border-radius: 20px;
  padding: 5px;
}
.search {
  height: 32px;
  width: 90%;
  border-radius: 24px;
}
.search:focus {
  outline:thin solid #ada9a9;
  padding:0px 10px;
}
.wrapper{
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items:center;
  gap: 5px;
  margin: auto;
}
.button-wrapper{
  display:flex;
  justify-content: flex-end;
}
.label{
  align-self: left;
}
</style>