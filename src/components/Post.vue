<script setup lang="ts">
import { ref } from "vue";

// If more time:
// - Add form validation for title and body
// - Consolidate the editing section with the addPost component
// - Add loading state while submitting
// - Handle errors from the API
// - Allow user to like or dislike posts

type Post = {
  id: number;
  title: string;
  body: string;
  views: number;
  tags: string[];
  reactions: {
    likes: number;
    dislikes: number;
  };
};

const props = defineProps<{
  post: Post;
}>();

const editableTitle = ref(props.post.title);
const editableBody = ref(props.post.body);
const updatePost = (postId: number) => {
  fetch(`https://dummyjson.com/posts/${postId}`, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      title: editableBody.value,
      body: editableBody.value,
    }),
  });
  isEditing.value = false;
};
const postClasses =
  "bg-white border border-[#E3E8EB] rounded-2xl px-4 py-3 shadow-[0px_1px_9px_2px_rgba(193,194,198,0.15)] text-left flex flex-col gap-3";

const isEditing = ref(false);
</script>

<template>
  <div v-if="!isEditing" :class="postClasses">
    <div class="flex justify-between items-start">
      <div class="font-bold">{{ editableTitle }}</div>
      <img src="../assets/edit.svg" @click="isEditing = true" class="shrink-0 cursor-pointer mt-1.5 ml-2" />
    </div>
    <div class="text-[#83888F] text-sm line-clamp-3">{{ editableBody }}</div>
    <div class="flex justify-start gap-2">
      <span v-for="(tag, index) in post.tags" :key="index" class="text-blue-900 text-xs bg-blue-100 rounded-lg px-2">
        #{{ tag }}<span v-if="index < post.tags.length - 1"> </span>
      </span>
    </div>
    <div class="flex justify-between mt-1">
      <div class="flex">
        <img src="../assets/thumb_up.svg" />
        <span class="text-[#83888F] text-xs ml-1">{{ post.reactions.likes }}</span>
      </div>
      <div class="flex">
        <img src="../assets/thumb_down.svg" />
        <span class="text-[#83888F] text-xs ml-1">{{ post.reactions.dislikes }}</span>
      </div>
      <div class="flex">
        <img src="../assets/views.svg" />
        <span class="text-[#83888F] text-xs ml-1">{{ post.views }}</span>
      </div>
    </div>
  </div>
  <div v-else :class="postClasses">
    <input v-model="editableTitle" class="border border-gray-400 rounded-md px-2 font-bold text-gray-800" type="text" />
    <textarea
      v-model="editableBody"
      rows="3"
      class="border border-gray-400 text-gray-700 rounded-md px-2 text-sm"
    ></textarea>
    <div class="flex justify-end">
      <img @click="isEditing = false" src="../assets/clear.svg" class="cursor-pointer" />
      <img @click="updatePost(post.id)" src="../assets/check.svg" class="cursor-pointer" />
    </div>
  </div>
</template>
