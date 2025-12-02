<template>
  <div>
    <h1 class="title">Burger <span>house</span>&trade;</h1>
    <hr />
    <div class="main">
      <h3>Our burgers:</h3>
      <div class="menu">
        <Burger v-for="(burger, index) in burgers" :burger="burger" v-model:count="count[index]" />
      </div>

      <div class="order-info">
        <h3>Order information:</h3>
        <p>
          Total:
          {{ count.reduce((sum, count, index) => sum + burgers[index].price * count, 0) }}kr
        </p>
        <label>Name</label>
        <input placeholder="Name" v-model="deliveryDetails.name" />
        <br />
        <label>Email</label>
        <input placeholder="Email" v-model="deliveryDetails.email" />
        <br />
        <label>Address</label>
        <div class="address">
          <input placeholder="Address" v-model="deliveryDetails.address" />
          <input placeholder="Nr" type="number" v-model.number="deliveryDetails.houseNumber" />
        </div>
        <br />
        <label>Gender</label>
        <label class="container">
          <input type="radio" name="gender" value="male" v-model="deliveryDetails.gender" />
          Male
        </label>
        <label class="container">
          <input type="radio" name="gender" value="female" v-model="deliveryDetails.gender" />
          Female
        </label>
        <label class="container">
          <input type="radio" name="gender" value="other" v-model="deliveryDetails.gender" />
          Other / prefer not to say
        </label>
        <div class="map" ref="map" @click="onLocationSelected">
          <div
            :style="{
              position: 'relative',
              width: '10px',
              height: '10px',
              left: `${selectedLocation.x * mapWidth - 5}px`,
              top: `${selectedLocation.y * mapHeight - 5}px`,
              background: 'red',
              borderRadius: '50%',
            }"
          ></div>
        </div>
        <button @click="addOrder">Place order</button>
      </div>
    </div>
  </div>
</template>

<script>
import Burger from "../components/Burger.vue";
import io from "socket.io-client";
import burgers from "@/assets/menu.json";

const socket = io("localhost:3000");

export default {
  name: "HomeView",
  components: { Burger },
  data() {
    return {
      mapWidth: 0,
      mapHeight: 0,
      burgers,
      selectedLocation: { x: 0, y: 0 },
      count: [0, 0, 0],
      deliveryDetails: {
        name: "",
        email: "",
        address: "",
        houseNumber: 0,
        gender: "male",
      },
    };
  },
  mounted() {
    this.mapWidth = this.$refs.map.offsetWidth;
    this.mapHeight = this.$refs.map.offsetHeight;
  },
  methods: {
    addOrder(event) {
      let order = {
        orderId: getOrderNumber(),
        deliveryInformation: {
          name: this.deliveryDetails.name,
          email: this.deliveryDetails.email,
          address: `${this.deliveryDetails.address} ${this.deliveryDetails.houseNumber}`,
          gender: this.deliveryDetails.gender,
          location: this.selectedLocation,
        },
        items: this.count
          .map((c, i) => {
            return {
              item: burgers[i].name,
              count: c,
            };
          })
          .filter((x) => x.count != 0),
      };

      socket.emit("addOrder", order);

      console.log(order);
    },
    onLocationSelected(event) {
      const rect = event.currentTarget.getBoundingClientRect();
      this.selectedLocation = {
        x: (event.clientX - rect.left) / rect.width,
        y: (event.clientY - rect.top) / rect.height,
      };
    },
  },
};

const getOrderNumber = () => {
  return Math.floor(Math.random() * 100000);
};
</script>

<style scoped>
button {
  background-color: #2dbc45ff;
  border: none;
  color: white;
  padding: 1rem;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
  border-radius: 0.1rem;
}
.title span {
  font-weight: 100;
}

.main {
  justify-self: anchor-center;
}

.menu {
  display: grid;
  grid-template-columns: auto auto auto;
  gap: 2rem;
}

.order-info {
  display: flex;
  flex-direction: column;
  width: 40rem;
  text-align: start;
  margin-block: 1rem;
  padding: 1rem;
  border: 1px beige solid;
  justify-self: center;
}

.order-info input {
  padding: 10px;
}

.order-info .address {
  display: grid;
  grid-template-columns: 80% 20%;
}

.map {
  background-image: url("/img/polacks.jpg");
  background-size: contain;
  cursor: crosshair;
  aspect-ratio: 16/9;
}
</style>
