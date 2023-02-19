<template>
  <div>
    <div class="container" v-if="layout==='grid'">
      <div class="book">
        {{displayTitle}}
      </div>
    </div>
    <div class="list-item-container" v-else >
      <input class="check" type="checkbox" name="id">
      <div class="book-list">
        {{livre.titre}}
      </div>
      <p class="title">{{livre.titre}}</p>
      <p class="price">{{livre.prix}} €</p>
      <div class="quantity">
        <button class="change-quantity" @click="decrementQuantite">-</button>
        <span>{{livre.qtestock}}</span>
        <button class="change-quantity" @click="incrementQuantite">+</button>
        <button class="delete" type="button" @click="supprimerLivre">Supprimer</button>
      </div>
    </div>
  </div>
</template>

<script setup >
import {defineProps, onMounted, computed, inject} from "vue"

const emitter = inject('emitter')

const props = defineProps(["livre", "layout"])

const title = props.livre.titre

// Lorsque le titre du livre est trop long, n'aficher que 40 caractères
const displayTitle = computed( () => {
      return title.length > 35 ? title.substr(0, 35) + " ... ": title
    }
)

onMounted( () => {
   console.log(props.layout)
 })
const emit = defineEmits(['selected', 'delete', 'decrement', 'increment'])
// supprimet un livre
const supprimerLivre = () => {
  console.log(props.livre)
  emit('selected')
  emit('delete')
}
// diminuer la quantité d'un livre
const decrementQuantite = () => {
  console.log("decrement ...")
  emit('selected')
  emit('decrement')
}
// diminuer la quantité d'un livre
const incrementQuantite = () => {
  console.log("Increment ...")
  emit('selected')
  emit('increment')
}
</script>

<style scoped>
.container{
  padding: 5px;
  position: relative;
  perspective: 700px;
  transform-style: perserve-3d;
}
.position{
  position: absolute;
}
.book{
  grid-column: 1/2;
  display: flex;
  justify-content:center;
  align-items:center;
  text-align: center;
  padding: 10px 0.125rem;
  height: 150px;
background: radial-gradient(ellipse farthest-corner at right bottom, #FEDB37 0%, #FDB931 8%, #9f7928 30%, #8A6E2F 40%, transparent 80%),
radial-gradient(ellipse farthest-corner at left top, #FFFFFF 0%, #FFFFAC 8%, #D1B464 25%, #5d4a1f 62.5%, #5d4a1f 100%);
  color: -webkit-linear-gradient(
      -72deg,
      #dedede,
      #ffffff 16%,
      #dedede 21%,
      #ffffff 24%,
      #454545 27%,
      #dedede 36%,
      #ffffff 45%,
      #ffffff 60%,
      #dedede 72%,
      #ffffff 80%,
      #dedede 84%,
      #a1a1a1
  );
  cursor: pointer;
  font-family: Ananda;
  -webkit-box-shadow: 5px 5px 0px 0px, 10px 10px 0px 0px #606060FF;
  -moz-box-shadow: 5px 5px 0px 0px, 10px 10px 0px 0px #606060FF;
  box-shadow: 5px 5px 0px 0px, 10px 10px 0px 0px #606060FF;
  border: 1px solid black;
}
/*list view */
.book-list{
  grid-column: 2/3;
  font-family: Ananda;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  padding: 10px 0.125rem;
  margin-left: 15px;
  height: 200px;
  width: 150px;
  background-color: orange;
  background: radial-gradient(ellipse farthest-corner at right bottom, #FEDB37 0%, #FDB931 8%, #9f7928 30%, #8A6E2F 40%, transparent 80%),
  radial-gradient(ellipse farthest-corner at left top, #FFFFFF 0%, #FFFFAC 8%, #D1B464 25%, #5d4a1f 62.5%, #5d4a1f 100%);
  color: var(--silver);
  -webkit-box-shadow: 5px 5px 0px 0px, 10px 10px 0px 0px #606060FF;
  -moz-box-shadow: 5px 5px 0px 0px, 10px 10px 0px 0px #606060FF;
  box-shadow: 5px 5px 0px 0px, 10px 10px 0px 0px #606060FF;
  border: 1px solid black;
}
.list-item-container{
  grid-column: 1/7;
  grid-column: 1/-1;
  display: grid;
  grid-template-columns: 1fr 4fr 8fr;
  grid-template-rows: 80px 50px 50px;
  row-gap: 10px;
  column-gap: 5px;
  margin: 10px 5px;
}
.check{
  grid-column: 1/2;
  grid-row: 2/3;
  margin: 0px 20px;
  width: 32px;
  height: 32px;
}
.title{
  text-align: left;
  font-size: 20px;
  grid-column: 3/10;
  font-family: Ananda;
}
.price{
  font-weight: bold;
  font-size: 32px;
  grid-column: 3/4;
}
.quantity{
  --button_size: 28px;
  font-size: 24px;
  grid-column: 3/12;
}
.delete{
  margin-left: 75px;
}
.change-quantity{
  margin: 0px 7.5px;
  height: var(--button_size);
  width: var(--button_size);
}
.close{
  align-self: flex-end;
  text-align: center;
  height: 20px;
  width: 20px;
}
</style>