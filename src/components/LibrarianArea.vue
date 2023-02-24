<template>
  <div class="actions-area">
    <SearchBook />
    <AddBookForm />
    <div>
      Actions :
    </div>
    <div class="actions" v-if="selection">
        <div class="action">
            <IconMinus @click="decrementall" />
        </div>
      <div class="action">
          <IconAdd @click="incrementall" />
      </div>
      <div class="action">
          <IconDelete @click="deleteall"/>
      </div>
    </div>
  </div>
</template>

<script setup>
import {reactive, ref, inject } from "vue";
import { useFetch } from '@vueuse/core'
import SearchBook from "./SearchBook.vue"
import AddBookForm from "./AddBookForm.vue"
import IconAdd from "./icons/IconAdd.vue"
import IconMinus from "./icons/IconMinus.vue"
import IconDelete from "./icons/IconDelete.vue"

const url = "https://webmmi.iut-tlse3.fr/~pecatte/librairies/public/2/livres"

const emitter = inject('emitter')

const livre = reactive({
  titre: "",
  qtestock: 1,
  prix: 1
})

const selection =  ref(false)
const ontype = () => {
  console.log(livre)
}

const ajouterLivre = () => {
  const headers = new Headers()
  headers.append("Content-Type", "application/json")
  const { isFetching, error, data } = useFetch(url, {
    method: "POST",
    headers: headers,
    body: JSON.stringify(livre)
  })
  emitter.emit('addbook')
  livre.titre = "";
  livre.prix = 0;
  livre.qtestock = 0;
}

const deleteall =  () => {
  emitter.emit('deleteall')
}

const incrementall =  () => {
  emitter.emit('incrementall')
}

const decrementall =  () => {
  emitter.emit('decrementall')
}
emitter.on('selection', ()=> {
  selection.value = true;
})
emitter.on('unselection', ()=> {
  selection.value = false;
})
</script>

<style scoped>
@-webkit-keyframes slide-top {
  0% {
    -webkit-transform: translateY(0);
    transform: translateY(0);
  }
  50% {
    -webkit-transform: translateY(-50px);
    transform: translateY(-50px);
  }
  100% {
    -webkit-transform: translateY(0);
    transform: translateY(0);
  }
}
@keyframes slide-top {
  0% {
    -webkit-transform: translateY(0);
    transform: translateY(0);
  }
  50% {
    -webkit-transform: translateY(-50px);
    transform: translateY(-50px);
  }
  100% {
    -webkit-transform: translateY(0);
    transform: translateY(0);
  }
}
@-webkit-keyframes slide-left {
  0% {
    -webkit-transform: translateX(0);
    transform: translateX(0);
  }
  50% {
    -webkit-transform: translateX(-50px);
    transform: translateX(-50px);
  }
  100% {
    -webkit-transform: translateX(0);
    transform: translateX(0);
  }
}
@keyframes slide-left {
  0% {
    -webkit-transform: translateX(0);
    transform: translateX(0);
  }
  50% {
    -webkit-transform: translateX(-50px);
    transform: translateX(-50px);
  }
  100% {
    -webkit-transform: translateX(0);
    transform: translateX(0);
  }
}
.actions-area{
  background-color: rgba(32,33,35,1);
  color: white;
  grid-column: 9/13;
  height: 100%;
  max-height: 100vh;
  padding: 20px 5px;
}
@media screen and (min-width: 360px) and (max-width: 1024px) {
  .actions-area{
    display: none;
  }
}
@media screen and (max-width: 486px) {
  .actions{
    position: fixed;
    bottom: 0;
    display: flex;
    justify-content: space-around;
    align-items: center;
    gap: 5px;
    background-color: #363434;
    width: 100%;
    height: 150px;
    border-radius: 15px 15px 0px 0px;
    -webkit-animation: slide-top 0.5s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;
    animation: slide-top 0.5s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;
  }
  .action{
    display: flex;
    justify-content: center;
    align-items: center;
    width: 55px;
    height: 55px;
    border-radius: 15px;
    background-color: #4b4949;
  }
}
.actions{
  position: fixed;
  right: 0;
  bottom: 0;
  margin-right: 15px;
  display: flex;
  justify-content: space-around;
  align-items: center;
  gap: 5px;
  background-color: #363434;
  width: 325px;
  height: 150px;
  border-radius: 15px;
  -webkit-animation: slide-left 0.5s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;
  animation: slide-left 0.5s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;
}
.action{
  display: flex;
  justify-content: center;
  align-items: center;
  width: 55px;
  height: 55px;
  border-radius: 15px;
  background-color: #4b4949;
}
</style>
