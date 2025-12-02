<template>
  <div
    :style="{
      background: /*selected ?*/ '#414434ff' /* :  '#2d2d2dff'*/,
      width: '20rem',
      borderRadius: '20px',
      textAlign: 'center',
    }"
  >
    <img :src="props.burger.imgUri" style="width: 100%; border-radius: 20px 20px 0 0" />
    <div style="padding: 10px; justify-items: center;">
      <h3>{{ props.burger.name }} - {{ props.burger.price }}kr</h3>
      <p>En god {{props.burger.name}}</p>
      <Badge color="#508a50">{{ burger.calories }} kcal</Badge>
      <Badge v-for="allergy in props.burger.allergies" color="#f16666" style="margin-left: 1rem;">{{allergy}}</Badge>
      <NumericUpDown v-model:value=count @update:value="onCountChanged"></NumericUpDown>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import Badge from "../components/Badge.vue";
import NumericUpDown from "./NumericUpDown.vue";
const props = defineProps({
  burger: {},
  count: {type: Number, default: 0}
});

const onCountChanged = (v) =>{
  count.value = v;
  emit('update:count', v);
}

const emit = defineEmits(['update:count']);
const count = ref(props.count);

</script>
