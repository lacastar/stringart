<template>
  <v-system-bar>
    <v-progress-linear
      v-model="progress"
      height="25"
      color="deep-orange"
      striped
    >
      <strong>{{ Math.ceil(progress) }}% </strong> ( {{ counter }} / {{ data.length }} )
    </v-progress-linear>
    </v-system-bar>
 
    
    <v-row>

      <v-col cols="12">
          <div class="text-center" @click="backclick">
            <span class="previous-item strikethrough text-red-darken-2">{{ previous }}</span>
          </div>
        </v-col>
        <v-col cols="12">


      <div class="text-center actual-item" @click="click">
        <v-btn
        variant="text"
        class="ma-2 h-100"
        color="light-green-accent-3"
        size="x-large"
      >{{ current }}
        <v-icon :icon="icon" end/>
      </v-btn>

        <!--<span class="actual-item text-light-green-accent-3">{{ current }}</span>-->
      </div>
      
      <div class="py-4" />
    </v-col>

      
        
        <v-col cols="12">
          <div class="text-center" @click="click">
            <span class="next-item text-light-green-accent-2">{{  next }}</span>
          </div>
        </v-col>
        
      </v-row>

 
  <!--<v-footer app>
    <v-progress-linear
      v-model="progress"
      height="25"
      color="deep-orange"
      striped
    >
      <strong>{{ Math.ceil(progress) }}% </strong> ( {{ counter }} / {{ data.length }} )
    </v-progress-linear>
  </v-footer>-->

</template>

<script setup>
  import { ref, computed, onMounted, watch  } from 'vue' 
  import resource from '../assets/resource.json'
  
  const data = ref(resource.data)
  const pins = resource.pins
  const divider = Math.floor(pins / 16)

  let counter = ref(0)

  // a computed ref
  const current = computed(() => {
    return counter.value < data.value.length ? translate(data.value[counter.value]) : "Kész!(?)"
  })

  const icon = computed(() => {
    if (counter.value >= data.value.length) return ""
    const value = translate(data.value[counter.value])
    
    switch (Math.floor(value / divider)) {
      case 0:
      case 15:
        return "mdi-arrow-top-left"
      case 1:
      case 2:
        return "mdi-arrow-up"
      case 3:
      case 4:
        return "mdi-arrow-top-right"
      case 5:
      case 6:
        return "mdi-arrow-right"
      case 7:
      case 8:
        return "mdi-arrow-bottom-right"
      case 9:
      case 10:
        return "mdi-arrow-bottom"
      case 11:
      case 12:
        return "mdi-arrow-bottom-left"
      default:
        return "mdi-arrow-left"
    }
    
  })

  const translate =(value)=>{
    const parts = pins / 4
    const part = Math.floor(value/parts)
    switch(part){
      case 2:
        return 3*pins/4 - (value-pins/4*2)
      case 3:
        return (pins-(value-pins/4*3)) % 148
      default:
        return value  
    }
  }

  const previous = computed(() => {
    
    return counter.value == 0 ? "-" : translate(data.value[counter.value - 1])
  })

  const next = computed(() => {
    return counter.value < data.value.length - 1 ? translate(data.value[counter.value + 1]) : "-"
  })

  const progress = computed(() => {
    return 100.0 * counter.value / data.value.length
  })

  const click = ()=>{
    if(counter.value < data.value.length) counter.value ++
  }
  const backclick = ()=>{
    if(counter.value > 0) counter.value --
  }

  //setting in local storage name item
watch(counter, (newVal) => {
  localStorage.setItem('counter', newVal)
})

onMounted(()=> {
  counter.value = Number(localStorage.getItem('counter')) || 0
})
  
</script>

<style scoped lang="sass">
  .actual-item
    font-size: 6rem
    font-weight: 300
  .v-btn--size-x-large
    font-size: 6rem
    button-border-radius: 12rem
  .previous-item
    font-size: 4rem
    font-weight: 100
  .next-item
    font-size: 4rem
    font-weight: 200
  .strikethrough
    position: relative
  .strikethrough:before
    position: absolute
    content: ""
    left: 0
    top: 50%
    right: 0
    border-top: 0.1rem solid
    border-color: inherit
  
  

</style>