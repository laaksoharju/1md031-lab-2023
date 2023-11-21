<template>
        <div class="burgare1">        
            <h2><span id="idname">{{burger.pName}}</span></h2>
            <img v-bind:src="burger.url" alt="view" style="width: 270px;">
            <p> Kalorier: {{ burger.kCal }} </p>
            <p v-if="burger.gluten && burger.lactose" class="bold"> Innehåller gluten och laktos</p>
            <p v-else-if="burger.gluten" class="bold"> Innehåller gluten </p>
            <p v-else-if="burger.lactose" class="bold"> Innehåller laktos </p>

            <p>Mängd: {{ this.amountBurgers }} </p>
            <div>
            <button id="decreaseButton" v-on:click="decreaseBurgers"> - </button>
            <button id="increaseButton" v-on:click="increaseBurgers"> + </button>
            </div>
        </div>
  </template>
  
  <script>
  export default {
    name: 'OneBurger',
    props: {
      burger: Object,
      required: true
    },
    data: function () {
  return {
    amountBurgers: 0
  }

},
  methods: {
  increaseBurgers: function() {
        this.amountBurgers += 1;
    this.addBurger();
    },
    decreaseBurgers: function() {
      if (this.amountBurgers > 0) {
    this.amountBurgers-= 1; 
    this.addBurger(); 
    }
  },
  addBurger: function () {
  this.$emit('orderedBurger', { name:   this.burger.pName, 
                                amount: this.amountBurgers}
  );
},
  }
  }  
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  
  </style>
  