<template>
  <div class="modal-wrapper">
    <div class="modal">
      <div class="icon-wrapper">
        <IconClose class="close" @click="close"/>
      </div>
      <div class="container">
        <div class="book">
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
  </div>
</template>

<script setup>
import {inject, toRef, watch} from "vue";
import IconClose from "./icons/IconClose.vue"

const emitter = inject('emitter')
const props = defineProps(['livre'])
// define a reactive property
const livreRef = toRef(props, 'livre')
// watch if the property changed
watch(livreRef, (newValue, oldValue) => {
  console.log(newValue, oldValue)
  livreRef.value = newValue
})
const emit = defineEmits(['close', 'delete', 'decrement', 'increment'])
const close = () => {
  emit('close')
}
// supprimet un livre
const supprimerLivre = () => {
  console.log(props.livre)
  emit('delete')
}
// diminuer la quantité d'un livre
const decrementQuantite = () => {
  console.log("decrement ...")
  emit('decrement')
}
// diminuer la quantité d'un livre
const incrementQuantite = () => {
  console.log("Increment ...")
  emit('increment')
}
// // lorsque la décrémentation a réussi
// emitter.on('remove', () => {
//   livreRef.qtestock -= 1
//   console.log("Succ", livreRef)
// })
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
  --width: 600px;
  --height: 300px;
  /*margin: auto;*/
  position: relative;
  width: var(--width);
  height: var(--height);
  border-radius: 15px;
  background-color: #424242;
  color: #868686;
  z-index: 100;
}
@media screen and (max-width: 740px) {
  .modal{
    --width: 500px;
    --height: 270px;
    /*margin: auto;*/
    position: relative;
    width: var(--width);
    height: var(--height);
    border-radius: 15px;
    background-color: #424242;
    color: #868686;
    z-index: 100;
  }
}
@media screen and (min-width: 360px) and (max-width: 486px) {
  .modal{
    --width: 350px;
    --height: 270px;
    /*margin: auto;*/
    position: relative;
    width: var(--width);
    height: var(--height);
    border-radius: 15px;
    background-color: #424242;
    color: #868686;
    z-index: 100;
  }
  .title{
    text-align: left;
    font-size: 10px;
    grid-column: 2/3;
    font-family: Ananda;
    padding: 10px;
    margin-left: 5px;
    color: #e8e3e3;
  }
  .price{
    font-weight: bold;
    font-size: 15px;
    grid-column: 2/3;
    padding-top: 50px;
    margin-left: 5px;
    color: #E8E3E3FF;
  }
}
.book{
  font-family: Ananda;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  padding: 10px 0.125rem;
  margin-left: 15px;
  height: 200px;
  width: 150px;
  /*background-color: orange;*/
  background: radial-gradient(ellipse farthest-corner at right bottom, #FEDB37 0%, #FDB931 8%, #9f7928 30%, #8A6E2F 40%, transparent 80%),
  radial-gradient(ellipse farthest-corner at left top, #FFFFFF 0%, #FFFFAC 8%, #D1B464 25%, #5d4a1f 62.5%, #5d4a1f 100%);
  color: #E8E3E3FF;
  -webkit-box-shadow: 5px 5px 0px 0px, 10px 10px 0px 0px #606060FF;
  -moz-box-shadow: 5px 5px 0px 0px, 10px 10px 0px 0px #606060FF;
  box-shadow: 5px 5px 0px 0px, 10px 10px 0px 0px #606060FF;
  border: 1px solid black;
}
.container{
  display:grid;
  grid-template-columns: 1fr 3fr;
  grid-template-rows: 80px 50px 50px;
  row-gap: 10px;
  column-gap: 5px;
}
.title{
  text-align: left;
  font-size: 20px;
  grid-column: 2/3;
  font-family: Ananda;
  padding: 20px;
  margin-left: 5px;
  color: #e8e3e3;
}
.price{
  font-weight: bold;
  font-size: 32px;
  grid-column: 2/3;
  padding: 20px;
  margin-left: 5px;
  color: #E8E3E3FF;
}
.quantity{
  --button_size: 28px;
  font-size: 24px;
  grid-column: 2/3;
  font-weight: 500;
  color: #E8E3E3FF;
  margin-left: 10px;
  padding: 20px;
}
.delete{
  margin-left: 75px;
}
.change-quantity{
  margin: 0px 7.5px;
  height: var(--button_size);
  width: var(--button_size);
}
.icon-wrapper{
  display:flex;
  justify-content: flex-end;
  margin: 10px 20px;
}
.close{
  display: flex;
  justify-content: flex-end;
  height: 20px;
  width: 20px;
}
</style>