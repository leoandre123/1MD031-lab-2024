<template>
  <span
    :style="{
      backgroundColor: props.color,
      color: 'white',
      padding: '4px 8px',
      textAlign: 'center',
      borderRadius: '5px',
    }"
  >
    <slot></slot>
  </span>
</template>

<script setup>
const props = defineProps(["color"]);
</script>
