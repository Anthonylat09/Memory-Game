<script>
export default {
    props: {
        matched: {
            type: Boolean,
            default: false
        },

        position: {
            type: Number,
            required: true
        },

        value: {
            type: String,
            required: true
        },

        visible: {
            type: Boolean,
            default: false
        }
    },


    
    setup(props, context){
        const selectCard = () => {
            context.emit('select-card', {
                position: props.position,
                faceValue: props.value
            })
        }

        return {
            selectCard
        }
    }
    
}

</script>

<template>
    <div class="card" @click="selectCard">
        <div v-if="visible" class="card-face is-front">
            <img :src="`/images/${value}.png`" :alt="value"/>
        </div>
        <div v-else class="card-face is-back">
        </div>
    </div>
    
</template>

<style>
.card{
  position: relative;
}

.card-face {
  width: 100%;
  height: 100%;
  position: absolute;
  border-radius: 10px;
  justify-content: center;
}

.card-face.is-front {
    background-image: url('../../public/images/cardsfront.png');
    color: white;
}

.card-face.is-back {
    background-image: url('../../public/images/cardsback.png');
    color: white;
}
</style>