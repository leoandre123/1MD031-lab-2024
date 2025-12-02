<template>
<div style="display: flex; margin:1rem;">
    <button @click="decrement">-</button>
    <p>{{ value }}</p>
    <button @click="increment">+</button>
</div>
</template>

<script setup>
import { ref, watch } from 'vue';

const props = defineProps({
  value: { type: Number, default: 0 }
});
const emit = defineEmits(['update:value']);

const value = ref(props.value)

function update(v) {
  value.value = v
  emit('update:value', v)
}

function increment() {
  update(Math.min(value.value + 1, 99))
}
function decrement() {
  update(Math.max(value.value - 1, 0))
}

watch(() => props.value, v => (value.value = v))

</script>


<style scoped>
div {
  display: flex;
  align-items: center;
  gap: 0.25rem;       
}

button,
p {
  display: flex;           
  align-items: center;
  justify-content: center; 
  background: #5f7a5f;
  color: white;
  border: none;
  padding: 0.25rem 0.5rem;
  margin: 0;
}

button{
    border-radius: 50%;
    width: 2rem;
    height: 2rem;
    cursor: pointer;
}
button:hover{
background: #314131;
}

p {
    border-radius: 0.15rem;
  width: 2rem;
}
</style>