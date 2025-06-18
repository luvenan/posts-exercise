<script setup lang="ts">
import Post from "./Post.vue";
import { ref, onMounted, onUnmounted } from "vue";

type Post = {
  id: number;
  title: string;
  body: string;
};

const posts = ref<Post[]>([]);
const page = ref(0);
const loading = ref(false);
const allLoaded = ref(false);

const getPosts = async (limit: number = 20, skip: number = 0) => {
  const res = await fetch(
    `https://dummyjson.com/posts?limit=${limit}&skip=${skip}`
  );
  const data = await res.json();
  posts.value = data.posts;
  return posts.value;
};

const loadMorePosts = async () => {
  const limit = 5;
  if (loading.value || allLoaded.value) return;
  loading.value = true;
  const newPosts = await getPosts(limit, page.value * limit);
  if (!newPosts.length) {
    allLoaded.value = true;
  } else {
    posts.value.push(...newPosts);
    page.value++;
  }

  loading.value = false;
};

onMounted(async () => {
  await getPosts();
  window.addEventListener("scroll", handleScroll);
});

onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll);
});

const handleScroll = () => {
  const scrollPosition = window.innerHeight + window.scrollY;
  const bottom = document.documentElement.scrollHeight - 100;

  if (scrollPosition >= bottom) {
    loadMorePosts();
  }
};
</script>

<template>
  <Post
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
    :body="post.body"
  />
</template>
