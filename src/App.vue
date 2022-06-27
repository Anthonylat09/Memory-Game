<template>
  <h1>Memory</h1>
  <section class="game-board">
    <CardComponent 
      v-for="(card, index) in cardList" 
      :key="`card-${index}`" 
      :matched="card.matched"
      :value="card.value"
      :visible="card.visible"
      :position="card.position"
      @select-card="flipCard"
    />
  </section>
  <h2>{{ status }}</h2>
  <p>{{remainingPairs}}</p>
  <button @click="shuffleCards">shuffle</button>
</template>

<script>
import _ from 'lodash'
import { computed, ref, watch } from 'vue'
import CardComponent from './components/CardComponent'

export default {
  name: 'App',
  components: {
    CardComponent
  },
  setup(){
    const cardList = ref([]) // Array containing the cards
    const userSelection = ref([])

    const status = computed(() => {
      if (remainingPairs.value === 0) {
        return 'Player wins!'
      } else {
        return `Remaining Pairs: ${remainingPairs.value}`
      }
    })


    const remainingPairs = computed(() => {
      const remainingCards =  cardList.value.filter(
        card => card.matched === false
        ).length

      return remainingCards / 2
    })


    const shuffleCards = () => {
      cardList.value = _.shuffle(cardList.value)
    }

    /**
     * Puts the values in the cards
     */
    for(let i = 0; i < 16; i++){
      cardList.value.push({
        value: i,
        visible: false,
        position: i,
        matched: false
      })
    }

    /**
     * Handles card flipping
     * @param {*} payload 
     */
    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true

      if (userSelection.value[0]) {
        userSelection.value[1] = payload
      } else {
        userSelection.value[0] = payload
      }
    }

    /**
     * Monitors the state of the user selection
     */
    watch(userSelection, (currentValue) => {
      if (currentValue.length === 2){
        const cardOne = currentValue[0]
        const cardTwo = currentValue[1]

        if (cardOne.faceValue === cardTwo.faceValue){
          status.value = 'Matched'
          cardList.value[cardOne.position].matched = true
          cardList.value[cardTwo.position].matched = true

        } else {
          status.value = 'Mismatched'
          cardList.value[cardOne.position].visible = false
        cardList.value[cardTwo.position].visible = false
        }

        

        userSelection.value.length = 0
      }
    }, { deep: true })

    return {
      cardList,
      flipCard,
      userSelection,
      status,
      remainingPairs,
      shuffleCards
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.card{
  border: 5px solid #ccc;
}

.game-board{
  display: grid;
  grid-template-columns: 100px 100px 100px 100px;
  grid-template-rows: 100px 100px 100px 100px;
  grid-column-gap: 30px;
  grid-row-gap: 30px;
  justify-content: center;
}
</style>
