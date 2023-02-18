<template>
  <form class="book-form" @submit.prevent="ajouterLivre" method="post">
    <p class="input-wrapper">
      <label class="label" for="titre">Titre</label>
      <input class="input"
             type="text"
             id="titre"
             name="titre"
             v-model="livre.titre"
             placeholder="Titre du livre ici ..."
             @input="ontype">
    </p>
    <p class="input-wrapper">
      <label class="label" for="quantite">Quantité en Stock</label>
      <input class="input"
             type="number"
             min="1"
             id="quantite"
             name="quantite"
             v-model="livre.qtestock"
             placeholder="Nombre de livres en stock"
             @input="ontype" >
    </p>
    <p class="input-wrapper">
      <label class="label" for="prix">Prix</label>
      <input class="input"
             type="number"
             min="1"
             id="prix"
             name="prix"
             v-model="livre.prix"
             placeholder="Prix du livre ici ..."
             @input="ontype" >
    </p>
    <button class="add-button" type="submit">
      Ajouter
    </button>
  </form>
</template>

<script setup>
import {reactive, ref, inject } from "vue";
import { useFetch } from '@vueuse/core'

const url = "https://webmmi.iut-tlse3.fr/~pecatte/librairies/public/2/livres"

const emitter = inject('emitter')

const livre = reactive({
  titre: "",
  qtestock: 1,
  prix: 1
})

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
  livre.prix = 1;
  livre.qtestock = 1;
}

</script>

<style scoped>
.input {
  height: 32px;
  width: 75%;
  border-radius: 24px;
  padding-left: 10px;
}
.input:focus {
  outline: thin solid;
  padding-left: 10px;
}
.book-form{
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-top: 50px;
}
.add-button{
  flex: none;
  border-radius: 15px;
  align-self: center;
  width: 100px;
}
.input-wrapper{
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 5px;
  width: 90%;
}
.label{
  align-self: flex-start;
  padding-left: 20px;
}
</style>