<template>
  <div class="burger">
                        <h3> {{ burger.name }} </h3>
                        <img v-bind:src="burger.url">
                        <p>Antal beställda: {{ amountOrdered }} </p>
                        <button type="add" v-on:click="increaseOrder">Lägg till burgare</button>
                        <button type="decrease" v-on:click="decreaseOrder">Ta bort burgare</button>
                        <ul>
                            <li>{{ burger.kCal }} kCal</li>
                            <li v-if="burger.gluten" class="ingredient">Innehåller gluten </li>
                            <li v-else> Glutenfri</li>
                            <li v-if="burger.lactose" class="ingredient"> Innehåller laktos </li>
                            <li v-else> Laktosfri </li>
                        </ul>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
  return {
    amountOrdered:0,
  }
},
  methods:{
    increaseOrder:function(){
      this.amountOrdered+=1;
      this.$emit("orderedBurger", {name:this.burger.name,
                                  amount: this.amountOrdered
      });
    },
    decreaseOrder:function(){
      this.amountOrdered-=1;
      this.$emit("orderedBurger", {name:this.burger.name,
                                  amount: this.amountOrdered
      });
    },
  
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.burger img{
    width:200px;
  
}
/*/HUR SKA JAG FÅ MED DENNA/*/
.ingredient {
    font-weight: bold;
    color:red;

}

</style>