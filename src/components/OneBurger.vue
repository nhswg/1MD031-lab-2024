<template>
  <div class="box">
    <h3>{{ burger.name }}</h3>
    <div class="image-container">
      <img v-bind:src="burger.img">
    </div>
    <ul>
      <li>{{ burger.kCal }} kCal</li>
      <li v-if="burger.lactose" class="ingredient">Contains Lactose</li>
      <li v-if="burger.gluten" class="ingredient">Contains Gluten</li>
    </ul>
    <p>
      Amount ordered: {{ amountOrdered }}
    </p>
    <button @click="increaseBurger">+</button>
    <button @click="decreaseBurger">-</button>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data() {
    return {
      amountOrdered: 0
    }
  },
  methods: {
    increaseBurger() {
      this.amountOrdered += 1;
      this.$emit('orderedBurger', {name: this.burger.name, amount: this.amountOrdered});
    },
    decreaseBurger() {
      if (this.amountOrdered > 0) {
        this.amountOrdered -= 1;
        this.$emit('orderedBurger', {name: this.burger.name, amount: this.amountOrdered});
      }
    }
  }
}
</script>

<style scoped>
.box {
  margin: auto;
  text-align: center;
}

.image-container {
  width: 150px;
  height: 100px;
  display: flex;
  margin: 0 auto 10px auto;
  align-items: center;
  border: 5px solid white;
  overflow: hidden;
}

.image-container img {
  width: 150px;
  height: 150px;
  object-fit: cover;
}

ul {
  text-align: left;
  padding-left: 0;
  list-style-position: inside;
}

</style>