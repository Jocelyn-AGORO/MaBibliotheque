<template>
  <div class="shelf-area">
    <h1 class="heading">Ma Bibliothèque</h1>
    <!--Rechercher un livre sur les écrans à taille moyenne -->
    <div class="search">
      <SearchBook />
    </div>
    <!--Ajouter un livre sur les écrans à taille moyenne -->
    <div class="add-book-medium">
      <button class="new-book" @click="isMedium=true">
        <IconBook />
        Nouveau Livre
      </button>
    </div>
    <!--Changer la présentation de l'affichage grille ou liste -->
    <div class="books-view">
      <IconList @click="changeView('list')" />
      <IconGrid @click="changeView('grid')" />
    </div>
    <div class="shelf" v-if="isEmpty">
        <BookComponent v-for="livre of livres"
                       :key="livre.id"
                       :livre="livre"
                       :layout="layout"
                       @click="toggleModal(livre)"
                       @selected="selectedItem(livre)"
                       @delete="deleteBook"
                       @decrement="updateQuantite"
                       @increment="incrementQuantite"
                       @action="addToList"/>
        <div v-if="isLoading">
          Chargement des livres ...
        </div>
      <div class="no-matches" v-if="!isFound">
        Aucun Livre ne correspond à cette recherche
      </div>
    </div>
    <div class="empty-library" v-else>
      <div>
        <button @click="isMedium=true">Nouveau Livre</button>
      </div>
    </div>
    <ModalComponent v-if="isOpen"
                    :livre="livreSelected"
                    @close="closeModal"
                    @delete="deleteBook"
                    @decrement="updateQuantite"
                    @increment="incrementQuantite"/>
    <ModalForm v-if="isMedium"
               @closemedium="isMedium=false"/>
  </div>
</template>

<script setup >
import {reactive, ref, onMounted, inject, watch} from "vue";
import { useFetch } from '@vueuse/core'
import ModalComponent from "../components/ModalComponent.vue"
import ModalForm from "../components/ModalForm.vue"
import BookComponent from "../components/BookComponent.vue"
import SearchBook from "../components/SearchBook.vue"
import IconGrid from "./icons/IconGrid.vue"
import IconList from "./icons/IconList.vue"
import IconBook from "./icons/IconBook.vue"

// url de l'api
const url = "https://webmmi.iut-tlse3.fr/~pecatte/librairies/public/2/livres"
// mode d'affichage des livres
const layout = ref('grid')
// valeur de la propriété grid du css selon le layout
const template_view = ref(6)
// livre selectionné
let livreSelected = reactive({})
// si il n'y a plus aucun livre
const isEmpty = ref(false)
// si le modal est visible
const isOpen = ref(false)
// si le support a un écran de taille moyenne
const isMedium = ref(false)
// liste des livres
const livres = reactive([])
// si les données sont en chargement
const isLoading = ref(true);
// si la recherche retourne des livres
const isFound = ref(true);
// les livres sélectionnés
const livresSelectionne =  reactive([]);
// inject the global emitter
const emitter = inject('emitter')
// changer les modes d'affichage
const changeView = (view_mode) => {
  layout.value = view_mode
  if( view_mode === 'grid') {
    template_view.value = 6
  }else {
    template_view.value = 1
  }
}
// si au moins un livre est selectionné
watch(livresSelectionne , ()=> {
  if(livresSelectionne.length > 0){
    emitter.emit('selection')
  }else {
    console.log("all unselected ...")
    emitter.emit('unselection')
  }
})
// ajouter un livre à la liste des livres séléectionnés
const addToList = (selected) => {
    if(selected.selected){
      livresSelectionne.push(selected.id)
    } else {
      const index = livresSelectionne.indexOf(selected.id)
      livresSelectionne.splice(index, 1)
      console.log(livresSelectionne)
    }
    console.log(selected, livresSelectionne)
}
// Ouvrir le modal
const toggleModal = (livre) =>{
  // Si le mode d'affichage est en grille on fait basculer l'état du modal
  if(layout.value === 'grid'){
    isOpen.value = !isOpen.value
  }
  livreSelected = livre
  console.log(livreSelected)
}
// assigner livreSelected à l'item de livre sélectionner
// dans le cas d'un affichage en liste
const selectedItem = (livre) => {
  livreSelected = livre
}
// fermer le modal
const closeModal = () => {
  isOpen.value = false;
}
// charger la liste des livres
const loadBooks = () => {
  const { isFetching, error, data } = useFetch(url, {
    afterFetch(ctx) {
      const data = JSON.parse(ctx.data)
      // console.log(data)
      data.map((livre) => {
        livres.push(livre)
      })
    }
  })
  isLoading.value = isFetching.value;
  isEmpty.value = livres.length === 0// isFound.value = livres.length === 0
  console.log(error.value)
}
// charger la liste des livres
const reloadBooks = () => {
  const { isFetching, error, data } = useFetch(url, {
    afterFetch(ctx) {
      const data = JSON.parse(ctx.data)
      // supprimer la liste de livres précédente
      livres.splice(0, livres.length)
      console.log(livres, "Before")
      // mettre à jour la liste des livres
      data.map((livre) => {
        livres.push(livre)
      })
      console.log(livres, "After")
    }
  })
}
// récupération de la liste des livres
onMounted( () => {
  loadBooks()
}
)
// effectuer la recherche
const actualSearch = (motcle) => {
  console.log(motcle, "Hello")
  const { isFetching, error, data } = useFetch(url+`?search=${motcle}`, {
    // mettre à jour la liste des livres
    afterFetch(ctx) {
      const data = JSON.parse(ctx.data)
      console.log(data)
      // supprimer l'ancien contenu
      livres.splice(0, livres.length)
      // remettre les livre correspondant à la recherche
      data.map((livre) =>{
        livres.push(livre)
      })
      isFound.value = ( livres.length !== 0 )
      console.log(isFound.value, data.length, ctx)
    }
  })
}
// recharger la page après suppression
const deleteBook = () => {
  console.log("Reloading ...", livreSelected, livreSelected.id)
  const headers = new Headers()
  headers.append("Content-Type", "application/json")
  const { isFetching, error, data } = useFetch(url+`/${livreSelected.id}`, {
    method: "DELETE",
    headers: headers
  })
  // refermer le modal
  isOpen.value = false
  console.log(livres)
  // recharger la bibliothèque
  setTimeout(()=> { reloadBooks() }, 125)
  console.log(livres)
}
const supprimerLivre = (id) => {
  const headers = new Headers()
  headers.append("Content-Type", "application/json")
  const { isFetching, error, data } = useFetch(url+`/${id}`, {
    method: "DELETE",
    headers: headers
  })
  console.log(livres)
  // recharger la bibliothèque
  setTimeout(()=> { reloadBooks() }, 125)
  console.log(livres)
}
// diminuer la quantité de livre de 1
// diminuer la quantité d'un livre
const updateQuantite = () => {
  console.log("Decrementing ...")
  const headers = new Headers()
  // copie les valeurs de l'objet actuel
  const bookToUpdate = {...livreSelected};
  // décrémenter la quantité de la copie
  bookToUpdate.qtestock -= 1
  console.log(bookToUpdate)
  headers.append("Content-Type", "application/json")
  const { isFetching, error, data } = useFetch(url, {
    method: "PUT",
    headers: headers,
    body: JSON.stringify(bookToUpdate)
  })
  console.log(error)
  if(!error.value){
    // décrémenter s'il n'y a pas eu d'erreur
    livreSelected.qtestock -= 1
    // Si la quantité est nulle, on supprime le livre
    if(livreSelected.qtestock === 0 ){
      deleteBook()
    }
    // recharger la bibliothèque
    emitter.emit('remove')
    reloadBooks()
    console.log('No error')
  }
}
// diminuer la quantité de livre de 1
// diminuer la quantité d'un livre
const incrementQuantite = () => {
  console.log("Decrementing ...")
  const headers = new Headers()
  // copie les valeurs de l'objet actuel
  const bookToUpdate = {...livreSelected};
  // décrémenter la quantité de la copie
  bookToUpdate.qtestock += 1
  console.log(bookToUpdate)
  headers.append("Content-Type", "application/json")
  const { isFetching, error, data } = useFetch(url, {
    method: "PUT",
    headers: headers,
    body: JSON.stringify(bookToUpdate)
  })
  console.log(error)
  if(!error.value){
    // décrémenter s'il n'y a pas eu d'erreur
    livreSelected.qtestock += 1
    // recharger la bibliothèque
    emitter.emit('remove')
    reloadBooks()
    console.log('No error')
  }
}
// résoudre l'évènement search émit lors de la recherche
emitter.on('search', actualSearch)
// recharge la liste des livre après un ajout
emitter.on('addbook', () => {
  setTimeout(reloadBooks, 125)
})
// supprimer plusieurs livres
emitter.on('deleteall', () => {
  console.log('Deleting all selected ...')
  livresSelectionne.map((id) =>{
    console.log(id)
    supprimerLivre(id)
  })
  // vider la liste des livres supprimés
  livresSelectionne.splice(0, livresSelectionne.length)
  reloadBooks()
})
</script>

<style scoped>
.shelf-area{
  --breakpoint: 'grid';
  background-color: rgba(32,33,35,1);
  height: 100vh;
  max-height: 100vh;
  grid-column: 1/9;
  padding: 10px;
  overflow-y: scroll;
  max-width: 100%;
  color: #e7e2e2;
}

@media screen and (min-width: 768px) and (min-width: 1024px) {
  .shelf{
    --fraction: v-bind(template_view);
    display:grid;
    grid-template-columns: repeat(var(--fraction), 1fr);
    gap:10px;
    color: #b2acac;
  }
}
@media screen and (min-width: 768px) and (max-width: 1024px) {
  .shelf{
    --fraction: v-bind(template_view);
    display:grid;
    grid-template-columns: repeat(var(--fraction), 1fr);
    gap:10px;
    color: #b2acac;
  }
}
@media screen and (max-width: 768px) {
  .shelf{
    display:grid;
    grid-template-columns: repeat(max(1, calc(v-bind(template_view) - 1)), 1fr);
    gap:10px;
    color: #b2acac;
  }
}
@media screen and (min-width: 360px) and (max-width: 640px) {
  .shelf{
    display:grid;
    grid-template-columns: repeat(max(1, calc(v-bind(template_view) - 2)), 1fr);
    gap:10px;
    color: #b2acac;
    width: 100vw;
  }
}
@media screen and (max-width: 425px) {
  .shelf{
    display:grid;
    grid-template-columns: repeat(max(1, calc(v-bind(template_view) - 3)), 1fr);
    gap:10px;
    color: #b2acac;
  }
}
.books-view {
  display:flex;
  justify-content: flex-end;
  align-items: center;
  gap: 5px;
}
.shelf-area::-webkit-scrollbar {
                        width: 8px;
                        height: 8px;
                      }
.shelf-area::-webkit-scrollbar-button {
  width: 5px;
  height: 5px;
}
.shelf-area::-webkit-scrollbar-thumb {
  background: #e1e1e1;
  border: 0px none #ffffff;
  border-radius: 50px;
}
.shelf-area::-webkit-scrollbar-thumb:hover {
  background: #ffffff;
}
.shelf-area::-webkit-scrollbar-thumb:active {
  background: #585656;
}
.shelf-area::-webkit-scrollbar-track {
  background: #666666;
  border: 0px none #ffffff;
  border-radius: 50px;
}
.shelf-area::-webkit-scrollbar-track:hover {
  background: #666666;
}
.shelf-area::-webkit-scrollbar-track:active {
  background: #333333;
}
.shelf-area::-webkit-scrollbar-corner {
  background: transparent;
}
.heading{
  display:flex;
  justify-content: center;
  font-family: Ananda;
  color:  #ECECF1;
  width: 100%;
  margin: 10px 0px;
}
.add-book-medium{
  display: flex;
  justify-content: flex-end;
  align-items: center;
  margin: 5px 0px;
}
.new-book{
  display: none;
}
.search{
  display: none;
}
.empty-library{
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  width: 100%;
}
.no-matches{
  text-align: center;
  height: 100%;
  width: 100%;
}
@media screen and (min-width: 360px) and (max-width: 1024px){
  .new-book{
    display: flex;
    justify-content: space-between;
  }
  .search{
    display: flex;
    justify-content: flex-end;
  }
  .search > div {
    width: 85%;
  }
}
</style>