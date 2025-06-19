<script setup lang="ts">
import { ref } from "vue";

const title = ref("");
const body = ref("");

// Emit event to parent (optional)
const emit = defineEmits<{
  (e: "newpost", payload: { title: string; body: string }): void;
  (e: "close", payload: void): void;
}>();

const handleSubmit = () => {
  if (!title.value || !body.value) return;
  fetch("https://dummyjson.com/posts/add", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      title: title.value,
      body: body.value,
      userId: 1, // Assuming a static userId for simplicity
    }),
  });
  emit("newpost", { title: title.value, body: body.value });
  title.value = "";
  body.value = "";
};
</script>

<template>
  <form @submit.prevent="handleSubmit">
    <div
      class="bg-white border border-[#E3E8EB] rounded-2xl px-4 py-2 w-[400px] shadow-[0px_1px_9px_2px_rgba(193,194,198,0.15)] text-left flex flex-col gap-2"
    >
      <input
        v-model="title"
        type="text"
        placeholder="Title"
        class="w-full border border-gray-400 rounded-md px-2 font-bold text-gray-800"
      />
      <textarea
        rows="3"
        v-model="body"
        placeholder="Write your post here..."
        class="w-full border border-gray-400 rounded-md px-2 text-sm text-gray-700"
      ></textarea>
      <div class="flex justify-end">
        <img @click="emit('close')" src="../assets/clear.svg" class="cursor-pointer" />
        <img @click="handleSubmit" src="../assets/check.svg" class="cursor-pointer" />
      </div>
    </div>
  </form>
</template>
