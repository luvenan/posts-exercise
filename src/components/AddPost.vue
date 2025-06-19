<script setup lang="ts">
  import { ref } from 'vue'

  const title = ref('')
  const body = ref('')

  // Emit event to parent (optional)
  const emit = defineEmits<{
    (e: 'newpost', payload: { title: string; body: string }): void
  }>()

  const handleSubmit = () => {
    if (!title.value || !body.value) return
    fetch('https://dummyjson.com/posts/add', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      title: title.value,
      body: body.value,
      userId: 1 // Assuming a static userId for simplicity
      })
    })
    emit('newpost', { title: title.value, body: body.value })
    title.value = '';
    body.value = '';
  }
</script>

<template>
  <div>
    <h2>Add Post</h2>
    <form @submit.prevent="handleSubmit">
      <div>
        <label>Title:</label>
        <input v-model="title" type="text" placeholder="Post title" />
      </div>
      <div>
        <label>Body:</label>
        <textarea v-model="body" placeholder="Post content"></textarea>
      </div>
      <button type="submit">Add Post</button>
    </form>
  </div>
</template>