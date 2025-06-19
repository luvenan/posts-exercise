<script setup lang="ts">
  import { ref } from "vue";

  type Post = {
    id: number;
    title: string;
    body: string;
  };

  const props = defineProps<{
    post: Post;
  }>();

  const editableTitle = ref(props.post.title);
  const editableBody = ref(props.post.body);
  const updatePost = (postId: number) => {
    fetch(`https://dummyjson.com/posts/${postId}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        title: editableBody.value,
        body: editableBody.value,
      })
    })
    isEditing.value = false;
  }



  const isEditing = ref(false);
</script>

<template>
  <div v-if="!isEditing">
    <div>{{ editableTitle }}
      <span @click="isEditing = true">edit</span>
    </div>
    <div >{{ editableBody }}</div>
  </div>
  <div v-else>
    <input v-model="editableTitle" type="text" />
    <textarea v-model="editableBody"></textarea>
    <button @click="updatePost(post.id)" type="submit">Done</button>
  </div>
</template>
