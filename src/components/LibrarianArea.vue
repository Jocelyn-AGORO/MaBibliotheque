<template>
  <div class="actions-area">
    <h2>Actions du gestionnaire</h2>
    <SearchBook />
    <AddBookForm />
    <div>
      Livres sélectionnés : {{livre.titre}}
    </div>
  </div>
</template>

<script setup>
import {reactive, ref, inject } from "vue";
import { useFetch } from '@vueuse/core'
import SearchBook from "./SearchBook.vue"
import AddBookForm from "./AddBookForm.vue"

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
  livre.prix = 0;
  livre.qtestock = 0;
}

</script>

<style scoped>
.actions-area{
  /*background-color: blue;*/
  background-color: rgba(32,33,35,1);
  color: white;
  grid-column: 9/13;
  height: 100%;
  max-height: 100vh;
}
.input {
  height: 32px;
  width: 75%;
  border-radius: 24px;
}
.input:focus {
  outline: thin solid;
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
  gap: 5px;
}
</style>

<!--<form class="book-form" @submit.prevent="ajouterLivre" method="post">-->
<!--<p class="input-wrapper">-->
<!--  <label for="titre">Titre</label>-->
<!--  <input class="input" type="text" id="titre" name="titre" v-model="livre.titre" @input="ontype">-->
<!--</p>-->
<!--<p class="input-wrapper">-->
<!--  <label for="quantite">Quantité en Stock</label>-->
<!--  <input class="input" type="number" min="1" id="quantite" name="quantite" v-model="livre.qtestock" @input="ontype" >-->
<!--</p>-->
<!--<p class="input-wrapper">-->
<!--  <label for="prix">Prix</label>-->
<!--  <input class="input" type="number" min="1" id="prix" name="prix" v-model="livre.prix" @input="ontype" >-->
<!--</p>-->
<!--<button class="add-button" type="submit">-->
<!--  Ajouter-->
<!--</button>-->
<!--</form>-->