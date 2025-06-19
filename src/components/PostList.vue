<script setup lang="ts">
import Post from "./Post.vue";
import { ref, onMounted, onUnmounted } from "vue";
import AddPost from "./AddPost.vue";

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

// If more time:
// - Add loading state for fetching posts
// - Add erorr handling for API requests

const posts = ref<Post[]>([]);
const showAddPost = ref(false);
const page = ref(0);
const loading = ref(false);
const allLoaded = ref(false);

const getPosts = async (limit: number = 20, skip: number = 0) => {
  const res = await fetch(`https://dummyjson.com/posts?limit=${limit}&skip=${skip}`);
  const data = await res.json();
  return data.posts;
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
  await loadMorePosts();
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

const showNewPost = (payload: { title: string; body: string }) => {
  posts.value.unshift({
    id: Math.floor(Math.random() * 10000), // Simulating an ID for the new post
    title: payload.title,
    body: payload.body,
    reactions: { likes: 0, dislikes: 0 },
    views: 0,
    tags: [],
  });
  showAddPost.value = false;
};
</script>

<template>
  <div class="w-[400px] flex justify-between mb-2 items-baseline">
    <div class="font-bold text-xl">Posts</div>
    <div @click="showAddPost = true" class="text-[#3679FF] text-sm cursor-pointer">Add post</div>
  </div>
  <div class="flex flex-col gap-3">
    <AddPost @newpost="showNewPost" @close="showAddPost = false" v-if="showAddPost" />
    <Post v-for="post in posts" :key="post.id" :post="post" />
  </div>
</template>
