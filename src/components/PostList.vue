<script setup lang="ts">
import Post from "./Post.vue";
import { ref } from "vue";

type Post = {
  id: number;
  title: string;
  body: string;
};

const posts = ref<Post[]>([]);

async function getPosts() {
  const res = await fetch("https://dummyjson.com/posts");
  const data = await res.json();
  posts.value = data.posts;
}

await getPosts();
</script>

<template>
  <Post
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
    :body="post.body"
  />
</template>
