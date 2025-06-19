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
  const postClasses ="bg-white border border-[#E3E8EB] rounded-2xl px-4 py-2 w-[400px] shadow-[0px_1px_9px_2px_rgba(193,194,198,0.15)] text-left flex flex-col gap-2"

  const isEditing = ref(false);
</script>

<template>
  <div v-if="!isEditing" :class="postClasses">
    <div class="flex justify-between items-center">
      <div class="font-bold">{{ editableTitle }}</div>
      <div @click="isEditing = true" class="shrink-0"><img src="../assets/edit.svg" class="cursor-pointer"/></div>
    </div>
    <div>{{ editableBody }}</div>
  </div>
  <div v-else :class="postClasses">
    <input v-model="editableTitle" class="border border-gray-400 rounded-md px-2 font-bold" type="text" />
    <textarea v-model="editableBody" rows="4" class="border  border-gray-400 rounded-md px-2 "></textarea>
    <div  class="flex justify-end">
      <img @click="isEditing=false" src="../assets/clear.svg" class="cursor-pointer" />
      <img @click="updatePost(post.id)" src="../assets/check.svg" class="cursor-pointer" />
    </div>
  </div>
</template>
