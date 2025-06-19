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
  <div v-if="!isEditing" class="bg-white border border-[#E3E8EB] rounded-2xl px-4 py-2 w-[400px] shadow-[0px_1px_9px_2px_rgba(193,194,198,0.15)] text-left">
    <div class="flex justify-between items-center">
      <div class="font-bold">{{ editableTitle }}</div>
      <div @click="isEditing = true"><img src="../assets/edit.svg" alt="Edit icon" /></div>
    </div>
    <div>{{ editableBody }}</div>
  </div>
  <div v-else>
    <input v-model="editableTitle" type="text" />
    <textarea v-model="editableBody"></textarea>
    <button @click="updatePost(post.id)" type="submit">Done</button>
  </div>
</template>
