<script setup lang="ts">
import Post from "./Post.vue";
import { ref, onMounted } from "vue";

type Post = {
  id: number;
  title: string;
  body: string;
};

const posts = ref<Post[]>([]);

const getPosts = async (limit: number = 20) => {
  const res = await fetch("https://dummyjson.com/posts?limit=" + limit);
  const data = await res.json();
  posts.value = data.posts;
};

onMounted(async () => {
  await getPosts();
});
</script>

<template>
  <Post
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
    :body="post.body"
  />
</template>
