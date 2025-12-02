<template>
  <div id="orders">
    <div id="orderList">
      <div v-for="(order, key) in orders" v-bind:key="'order' + key" class="order">
        <div>
          <p>#{{ key }}:</p>
        </div>
        <div>
          <p class="bold">
            {{ order.items.map((x) => `${x.count}x ${x.item}`).join(", ") }}
          </p>
          <p class="details">
            {{ `${order.deliveryInformation.name}, ${order.deliveryInformation.address}` }}
          </p>
        </div>
        <hr />
      </div>
      <button v-on:click="clearQueue">Clear Queue</button>
    </div>
    <div id="dots" ref="map">
      <div
        v-for="(order, key) in orders"
        v-bind:style="{
          left: order.deliveryInformation.location.x * mapWidth - 10 + 'px',
          top: order.deliveryInformation.location.y * mapHeight - 10 + 'px',
        }"
        v-bind:key="'dots' + key"
      >
        {{ key }}
      </div>
    </div>
  </div>
</template>
<script>
import io from "socket.io-client";
const socket = io("localhost:3000");

export default {
  name: "DispatcherView",
  data: function () {
    return {
      orders: null,
      mapWidth: 0,
      mapHeight: 0,
    };
  },
  mounted() {
    this.mapWidth = this.$refs.map.offsetWidth;
    this.mapHeight = this.$refs.map.offsetHeight;
  },
  created: function () {
    socket.on("currentQueue", (data) => {
      this.orders = data.orders;
      console.log(this.mapWidth);
      console.log(this.orders);
    });
  },
  methods: {
    clearQueue: function () {
      socket.emit("clearQueue");
    },
    changeStatus: function (orderId) {
      socket.emit("changeStatus", { orderId: orderId, status: "Annan status" });
    },
  },
};
</script>
<style scoped>
.order {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.order .bold {
  margin: 0;
  font-weight: 600;
  font-size: large;
}

.order .details {
  margin: 0;
  font-weight: 100;
  font-style: italic;
}

#orderList {
  top: 1em;
  left: 1em;
  position: absolute;
  z-index: 2;
  color: black;
  background: rgba(255, 255, 255, 0.5);
  padding: 1em;
  width: 20rem;
}
#dots {
  position: relative;
  margin: 0;
  padding: 0;
  background-repeat: no-repeat;
  cursor: crosshair;
  width: 1920px;
  height: 1078px;
  background-image: url("/img/polacks.jpg");
}

#dots div {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width: 20px;
  height: 20px;
  text-align: center;
}
</style>
