<template>
  <div>
    <header class="header-background">
      <img id="background" src="/img/fire.jpg">
      <h1 id="company-name">Welcome to Bob Burger</h1>
    </header>
    <main>
      <section id="selection">
        <h2>Select burger</h2>
        <div>
          This is where you execute burger selection
        </div>
        <div class="burger-grid">
          <OneBurger
            v-for="burger in burgers"
            v-bind:key="burger.name"
            v-bind:burger="burger"
            v-on:orderedBurger="addToOrder($event)"
          ></OneBurger>
        </div>
      </section>
      <section id="customer">
        <h2>Customer information</h2>
        <div>
          This is where you provide necessary information
        </div>
        <h3>Delivery information:</h3>
        <form>
          <p>
            <label for="fullName">Full name</label><br>
            <input type="text" id="fullName" v-model="fullName" required placeholder="First and Last name">
          </p>
          <p>
            <label for="email">E-mail</label><br>
            <input type="email" id="email" v-model="email" required placeholder="E-mail address">
          </p>
          <p>
            <label for="phone">Phone Number</label><br>
            <input type="number" id="phone" v-model="phone" required placeholder="Phone Number">
          </p>
          <p>
            <label for="payment">Payment options</label><br>
            <select id="payments" v-model="payment">
              <option>Credit card</option>
              <option>PayPal</option>
              <option>In-store</option>
              <option>Klarna</option>
            </select>
          </p>
          <div class="map-container">
            <div id="map" v-on:click="setLocation">
              <div class="dot" v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }">
                T
              </div>
          </div>
            Click on the map to set delivery location.
          </div>
          <p>
            <label for="gender">Gender</label><br>
            <input type="radio" id="male" v-model="gender" value="male">
            <label for="male">Male</label><br>
            <input type="radio" id="female" v-model="gender" value="female">
            <label for="female">Female</label><br>
            <input type="radio" id="no-answer" v-model="gender" value="no-answer">
            <label for="no-answer">Do not wish to provide</label>
          </p>
        </form>
      </section>
    </main>
    <button type="submit" v-on:click="placeOrder">
      <img src="/img/order.png" style="width: 20px;">
      Submit Order
    </button>
    <hr>
    <footer>
      &copy; Bob Burger
    </footer>
  </div>
</template>

<script>
import OneBurger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, kCal, img, gluten, lactose) {
  this.name = name;
  this.kCal = kCal;
  this.img = img;
  this.gluten = gluten;
  this.lactose = lactose;
}

// const burgers = [
//   new MenuItem("Cheeseburger", 250, "/img/cheese.png", true, true),
//   new MenuItem("Double Cheeseburger", 500, "/img/double_cheese.png", true, true),
//   new MenuItem("Triple Cheeseburger", 1000, "/img/triple_cheese.png", true, true)
// ];

export default {
  name: 'HomeView',
  components: {
    OneBurger
  },
  data: function () {
    return {
      burgers: menu,
      fullName: '',
      email: '',
      phone: '',
      payment: '',
      gender: '',
      orderedBurgers: {},
      location: { x: 0, y: 0 }
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };
      this.location.x = event.clientX - 10 - offset.x;
      this.location.y = event.clientY - 10 - offset.y;
      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: { x: this.location.x, y: this.location.y },
        orderItems: ["Beans", "Curry"]
      });
    },
    addToOrder(event) {
    this.orderedBurgers[event.name] = event.amount;
    console.log(this.orderedBurgers);
    },
    placeOrder() {
      console.log('Full Name:', this.fullName);
      console.log('Email:', this.email);
      console.log('Phone Number:', this.phone);
      console.log('Payment Method:', this.payment);
      console.log('Gender:', this.gender);
      console.log('Ordered Burgers:', this.orderedBurgers);
      console.log('Delivery Location:', this.location);

      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: {
          x: this.location.x,
          y: this.location.y
        },
        orderItems: this.orderedBurgers,
        customerInfo: {
          fullName: this.fullName,
          email: this.email,
          phone: this.phone,
          payment: this.payment,
          gender: this.gender
        }
      });
    },
    setLocation(event) {
      const offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };
      this.location.x = event.clientX - 10 - offset.x;
      this.location.y = event.clientY - 10 - offset.y;
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@700&family=Roboto:wght@400&display=swap');

body {
    font-family: 'Roboto', sans-serif;
    font-size: 12pt;
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
}

h1 {
    font-family: 'Montserrat', sans-serif;
    font-size: 30pt;
    text-transform: uppercase;
    text-shadow: 
      -1px -1px 0 white,
      1px -1px 0 white,
      -1px  1px 0 white,
      1px  1px 0 white;
}

.ingredient {
    font-weight: bold;
}

.header-background {
    margin: 10px;
    height: 200px;
    overflow: hidden;
}

.map-container {
  width: 100%;
  max-width: auto;
  height: 400px;
  overflow: scroll;
  border: 2px solid gray;
  margin-top: 10px;
}

#map {
    position: relative;
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
}

.dot {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width: 20px;
    height: 20px;
    text-align: center;
}

#company-name {
    position: absolute;
    margin-top: -120px;
    margin-left: 75px;
}

#background {
    opacity: 0.7;
    width: 100%;
    height: 100%;
}

#selection {
    background-color: black;
    color: white;
}

section {
    margin: 10px;
    padding: 20px;
    border: 10px dashed currentColor;
}

.burger-grid {
    display: grid;
    grid-gap: 20px;
    justify-content: center;
    grid-template-columns: 33.33% 33.33% 33.33%;
    padding: 20px;
}

button {
    margin-top: 10px;
    margin-left: 10px;
}

button:hover {
    background-color: #0cdd21a0;
    cursor: pointer;
}
</style>