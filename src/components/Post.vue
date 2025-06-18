<script setup lang="ts">
defineProps<{
  title: string;
  body: string;
}>();
</script>

<template>
  <div>{{ title }}</div>
  <div>{{ body }}</div>
</template>
