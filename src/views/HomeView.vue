<template>
    <main>
    <header>
        <h1 class="headertext">Stans bästa burgare</h1>
    </header>
    <section>
      <div class="wrapper">
      <Burger v-for="burger in burgers"
              v-bind:burger="burger" 
              v-bind:key="burger.pName"
              v-on:orderedBurger="addBurger($event)"/> 
      </div>
  </section>
 <!-- <body>
        <section style="margin: 0;"> 
            <p style="font-style: italic;">Beställ din burgare nedan</p>
        </section>
       <section id="blackwhite" style="margin-top: 0;">
    <div class="wrapper">
        <div class="burgare1">        
            <h2>Uppsala Special</h2>
            <img src="https://heygrillhey.com/static/47c08cf31c9c6df778aee3088264eddf/Smoked-Hamburgers-Feature.png" 
            alt="Very good burger" style="width: 200px;"><br>
            <ul>
                <li>Kött </li>
                <li>Tomat</li>
                <li>Rödlök</li>
                <li><span id="bold">Laktos</span></li>
            </ul>
        </div>
        <div class="burgare2">        
            <h2>Smash Burger</h2>
            <img src="https://media-cdn.tripadvisor.com/media/photo-s/25/18/0e/8a/original-smash-burger.jpg" 
            alt="Very good burger" style="width: 219px;"><br>
            <ul>
                <li>Smashad</li>
                <li>Två burgare</li>
                <li>Karamelliserad lök</li>
                <li><span id="bold">Gluten</span></li>
            </ul>
        </div>
        <div class="burgare2">        
            <h2>Tornet</h2>
            <img src="https://img.koket.se/standard-giant/smashburger-med-karamelliserad-lok-och-tryffelmajo.png.webp" 
            alt="Very good burger" style="width: 267px;"><br>
            <ul>
                <li>Två burgare</li>
                <li>Karamelliserad lök</li>
                <li>Tryffel</li>
                <li><span id="bold">Laktos och Gluten</span></li>
            </ul>
        </div>
    </div>
       </section>
      -->
       <section>   
        <h3> Delivery Information</h3> 
        <p>
            <label for="Name">Your Name</label><br>
            <input type="text" id="Name" v-model="name" required="required" placeholder="First- and last name">
        </p>
        <p>
            <label for="email">E-mail Adress</label><br>
            <input type="text" id="email" v-model="email" placeholder="E-mail">
        </p>
        <p>
            <label for="betalmedel">Betalmedel</label><br>
            <select id="betalmedel" v-model="payment">
                <option>Swish</option>
                <option>Kort</option>
                <option>Klarna</option>
            </select>
         </p>
        <p>
            <label for="Gender">Kön:</label><br>

            <label for="gender">Kille</label>
            <input type="radio" id="genderKille" checked="checked" v-model="gender" value="Kille"><br>

            <label for="gender">Tjej</label>
            <input type="radio" id="genderTjej" v-model="gender" value="Tjej"><br>

            <label for="gender">Annat</label>
            <input type="radio" id="genderAnnat" v-model="gender" value="Annat"><br>
        </p>
       </section>
       <h3>      Välj leveransadress nedan:</h3>
      <div class = "mapSmall">
      <div id="map" v-on:click="setLocation">
      <div id="dots"> 
      <div v-bind:style="{left: location.x + 'px', top: location.y + 'px'}">
          T 
      </div>
      </div>
      </div>
      </div>
      <section>   
        <button type="submit" class="button" v-on:click="submitForm">
            <img src="https://www.pngall.com/wp-content/uploads/12/Order-Now-Button-Vector.png"
            style="width: 150px;">
          </button>
       </section>
    <footer>
       <hr> &copy;	2023 gott med burgare AB
    </footer>
        </main>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'
import { toHandlers } from 'vue';

const socket = io();


function MenuItem(pName,kCal, lactose, gluten, url, amountBurgers){
   this.pName = pName;
   this.kCal = kCal;
   this.gluten = gluten;
   this.lactose = lactose;
   this.url = url;
   this.amountBurgers = amountBurgers;
}

const burgerArray = menu.map(item => new MenuItem(item.pName, item.kCal, item.lactose, item.gluten, item.url, 0));

/*
const burgerArray = [
  new MenuItem("Uppsala Special", 650, "https://heygrillhey.com/static/47c08cf31c9c6df778aee3088264eddf/Smoked-Hamburgers-Feature.png", 
  true, false),
  new MenuItem("Smash Burger", 700, "https://media-cdn.tripadvisor.com/media/photo-s/25/18/0e/8a/original-smash-burger.jpg", 
  false, false),
  new MenuItem("Tornet", 800, "https://img.koket.se/standard-giant/smashburger-med-karamelliserad-lok-och-tryffelmajo.png.webp", 
  true, true)
]
*/

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      //burgers: [ {name: "small burger", kCal: 250},
      //           {name: "standard burger", kCal: 450},
      //           {name: "large burger", kCal: 850}
      //         ]
      burgers: burgerArray,
      payment:'Swish',
      gender:"Kille",
      amountBurgers:0,
      orderedBurgers:{},
      location: { x: 0,
            y: 0
          }
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};

      this.location.x=event.clientX -10 - offset.x;
      this.location.y=event.clientY -20 - offset.y;
    },
    setLocation: function (event) {
      this.location.x=event.clientX -10 - event.currentTarget.getBoundingClientRect().left;
      this.location.y=event.clientY -20 - event.currentTarget.getBoundingClientRect().top;
    },
    addBurger: function (event) {
  this.orderedBurgers[event.name] = event.amount;
    },
    submitForm: function() {
      alert("Din order tillagas!");
            socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y },
                               orderItems: this.orderedBurgers,
                               customerDetails: {name: this.name, email: this.email, payment: this.payment, gender: this.gender}
                            }
                 );
                 const orderDetails = [
      this.name,
      this.email,
      this.payment,
      this.gender,
      JSON.stringify(this.orderedBurgers)
      ]
      console.log(orderDetails)
       }
  }
  }

</script>

<style>
  #map {
    background:url('../../public/img/polacks.jpg');
    width:1920px;
    height:1078px;
  }

  .mapSmall{
    width:  100%px;
    height: 400px;
    overflow: scroll;
  }
body {
    font-family: 'Times New Roman', Times, serif;
}
     .bold {
        font-weight: bold;
        color: brown;
     }
     #blackwhite {
        color: white;
        background-color: black;
     }
div {
    margin-left: 0;
} 
header {
    background: url("https://www.londonkoll.se/wp-content/uploads/2014/11/Pubar-London-1200x630px.jpg");
    background-size: cover;
    background-repeat: no-repeat;
    height: 200px;
    width: 100%;
    
    
  }
  .headertext {
    padding-top: 10px;
    padding-left: 10px;
    color: wheat;
  }
.button {
    margin: 10px 0 40px 0;
    color: wheat;
}
button:hover {
    background-color: grey;
    cursor: pointer;
}
section {
    border: 2px groove brown;
    border-radius: 15px;
    padding-left: 0;
    margin-top: 30px;
}
.wrapper {
    display: grid;
    grid-gap: 10px;
    grid-template-columns: 30% 30% 30%;
    background-color: black;
    color: wheat;
    border-radius: 13px;
}
#dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }

</style>
