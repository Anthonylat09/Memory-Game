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
  <button @click="restartGame">Restart Game</button>
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

    const restartGame = () => {
      shuffleCards()

      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          matched: false,
          position: index,
          visible: false
        }
      })
    }

    const cardItems = ['hiei', 'kurama', 'yusuke', 'kuwabara', 'genkai', 'keiko', 'toguro', 'sensui']
    /**
     * Puts the values in the cards
     */
    cardItems.forEach(item => {
      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        matched: false
      })

    cardList.value.push({
        value: item,
        visible: false,
        position: null,
        matched: false
      })
      
    })

  cardList.value = cardList.value.map((card, index) => {
    return {
      ...card,
      position: index
    }
  })

    /**
     * Handles card flipping
     * @param {*} payload 
     */
    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true

      if (userSelection.value[0]) {
        if (userSelection.value[0].position === payload.position && userSelection.value[0].faceValue === payload.faceValue){
          return
        } else {
          userSelection.value[1] = payload
        }
        
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
          cardList.value[cardOne.position].matched = true
          cardList.value[cardTwo.position].matched = true

        } else {
          setTimeout(() => 
          {cardList.value[cardOne.position].visible = false
          cardList.value[cardTwo.position].visible = false},
          1000
          )
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
      shuffleCards,
      restartGame
    }
  }
}
</script>

<style>

html, body {
  margin: 0;
  padding: 0;
}

h1 {
  margin-top: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  background-image: url('../public/images/back.jpg');
  height: 100vh;
}


.game-board{
  display: grid;
  grid-template-columns: repeat(4, 120px);
  grid-template-rows: repeat(4, 120px);
  grid-column-gap: 24px;
  grid-row-gap: 24px;
  justify-content: center;
}
</style>
