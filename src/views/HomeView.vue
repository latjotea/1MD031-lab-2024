<template>
<header class="whole-header">
            <img id=company-picture src="/img/company-picture.png">
            <h1 id="company-name">Välkommen till BurgerOnline</h1>
</header>
        <main>
            <section id="choice">
                <h2>Välj burgare</h2>
                    <p>Under denna sektion väljer du vilken burgare du är sugen på</p>
                    <ul>
                        <li> Vegetariska alternativ (Halloumi/Bönor) finns för samtliga burgare</li>
                    </ul>
                <div class="burgertypes">
                  <Burger
                    v-for="burger in burgers"
                    v-bind:burger="burger"
                    v-bind:key="burger.name"
                    v-on:orderedBurger="addToOrder($event)"/>

                </div>
            
            </section>

            <section id="order">
                <h3>Kundinformation</h3>
                    <p>Här ska du berätta lite om dig så att vi kan leverera burgaren till dig</p>
                    <h4> Leverans information:</h4>
                    <form>
                        <p>
                            <label for="firstlastname">Namn</label><br>
                            <input type="text" id="firstlastname" v-model="customer.name" required="required" placeholder="För- och efternamn">
                        </p>
                        <p>
                            <label for="email">Mail</label><br>
                            <input type="email" id="email" v-model="customer.mail" required="required"  placeholder="Mail-adress">
                        </p>
                        <div id="container-map">
                          <div id="map" v-on:click="setLocation">
                            Välj vart burgaren ska levereras. Din adress markeras med T. 
                          </div>
                          <div class="target" v-bind:style="{left: location.x + 'px', top: location.y + 'px', position: 'absolute'}">
                            T
                          </div>
                        </div>
                        <p>
                            <label for="payment"> Betalningsmetod: </label>
                            <select id="payment" v-model="customer.paymentMethod">
                                <option> Swish </option>
                                <option> Betalkort </option>
                                <option> PayPal </option>
                                <option> Betala kontant när burgaren anländer</option>
                            </select>
                        </p>
                        <h4>Kön:</h4>
                        <p>
                            <input type="radio" v-model="customer.gender" id="gender-female" name="kön" value="Kvinna" required="required">
                            <label for="gender-female">Kvinna</label>
                        </p>
                        <p>
                            <input type="radio" v-model="customer.gender" id="gender-male" name="kön" value="Man" required="required">
                            <label for="gender-male">Man</label>
                        </p>
                        <p>
                            <input type="radio" v-model="customer.gender" id="gender-else" name="kön" value="Annat" required="required">
                            <label for="gender-else">Vill inte ange</label>

                        </p>

                      </form>


            </section>
        </main>
        <button type="submit" v-on:click="submitOrder">
            <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTm7t5zqPqRBGUPtyWk2_2YuuzXZ1Dl1Gx_uw&s" style="width:30px; height:30px">
            Beställ!
        </button>
        <hr>
        <footer>
            &copy; Teas burgeria
        </footer>
</template>
<script>

function menuItems(name, url, kCal, gluten, lactose){
  return{
    name:name,
    url:url,
    kCal:kCal,
    gluten:gluten,
    lactose:lactose
  };  
}
const burgers=[
  menuItems("Jalapeno Dream","https://www.chilitochoc.com/wp-content/uploads/2022/10/the-ultimate-cheeseburger-with-pickled-jalapenos-1.jpg", 800, false, false),
  menuItems("Union Dream","https://www.vindulge.com/wp-content/uploads/2021/03/Western-Burgers-featured-image.jpg", 800, false, false),
  menuItems("Sugar Dream","https://i.dailymail.co.uk/1s/2020/05/27/15/28886608-8361335-image-a-39_1590589896163.jpg", 1300, true, true)
]



import { customRef } from 'vue';
import menu from '../assets/menu.json';
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'

const socket = io("localhost:3000");

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      customer:{name:"",
                mail:"",
                adress:"",
                houseNumber:"",
                paymentMethod:"Swish",
                gender:"Kvinna"
      },
      orderedBurger:{},
      location:{x:0, 
                y:0
      }
  
    };
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};

      this.location = {
                        x: event.clientX - offset.x,
                        y: event.clientY - offset.y};
    },

    submitOrder: function(){
      console.log("Kundinformation");
      console.log("Namn:", this.customer.name);
      console.log("Mail:", this.customer.mail);
      console.log("Adress:", this.customer.adress);
      console.log("Husnummer:", this.customer.houseNumber);
      console.log("Betalningsmetod:", this.customer.paymentMethod);
      console.log("Kön:", this.customer.gender)
      console.log("Beställning:", this.orderedBurger)

      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y },
                                orderItems: this.orderedBurger,
                                name:this.customer.name,
                                email:this.customer.mail,
                                paymentMethod: this.customer.paymentMethod,
                                gender:this.customer.gender
                              }
                 );

      },
      addToOrder:function(event){
        this.orderedBurger[event.name]=event.amount;
      }
    }
}
</script>

<style> 
  @import url('https://fonts.googleapis.com/css2?family=Oswald:wght@200..700&family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap');
body {
    font-family:'Oswald', sans-serif;
}
#container-map{
  height:500px;
  width:800px;
  overflow:scroll;
  position:relative;
}

#map{
  background: url("/img/polacks.jpg");
  height:1078px;
  width:1920px;
  overflow:scroll;
  position: relative;

}
.target{
  color:red;

}
.whole-header{
    padding:5px;
    margin:5px;
    height: 150px;
    overflow: hidden;

}

#company-picture{
    opacity: 0.7;
    width: 100%;
    height: auto;
    
}

#company-name{
    position: absolute;
   top: 50px;
   left:200px;
   width:40em;
        
}


.burgertypes{
    display: grid;
    grid-gap: 200px;
    grid-template-columns: 200px 200px 200px;
    background-color:black;
    color: white;
    padding: 10px;

}

.ingredient {
    font-weight: bold;
    color:red;

}

#choice {
    background-color: black;
    color: white;
}

button:hover {
    background-color: rgb(169, 233, 72);
    cursor: pointer;
}

button {  margin-left: 10px;
        margin-bottom:30px;
}
section div {
    margin: 10px;
    padding: 10px;
}

section{
   margin: 10px;
    padding: 10px;
    border: 5px dashed currentColor;
  
}


</style>