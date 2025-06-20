<script setup lang="ts">
import Post from "./Post.vue";
import { ref, onMounted, onUnmounted } from "vue";
import AddPost from "./AddPost.vue";
import { watch } from "vue";

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
// - Add loading spinner for fetching / searching posts
// - Show api error messages to the user

const posts = ref<Post[]>([]);
const showAddPost = ref(false);
const page = ref(0);
const loading = ref(false);
const allLoaded = ref(false);
const searchQuery = ref("");
const isSearching = ref(false);

const getPosts = async (limit = 20, skip = 0, search = "") => {
  try {
    let url = `https://dummyjson.com/posts`;
    if (search.trim()) {
      url += `/search?q=${encodeURIComponent(search.trim())}`;
      console.log("Searching for:", search.trim(), url);
    } else {
      url += `?limit=${limit}&skip=${skip}`;
    }
    const res = await fetch(url);
    if (!res.ok) {
      throw new Error(`Failed to fetch posts: ${res.status} ${res.statusText}`);
    }
    const data = await res.json();
    return data.posts || [];
  } catch (err) {
    console.error("Error fetching posts:", err);
    return [];
  }
};

const loadMorePosts = async () => {
  if (loading.value || allLoaded.value) return;
  if (searchQuery.value.trim()) return;

  loading.value = true;
  const limit = 5;
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

const triggerSearch = async () => {
  const query = searchQuery.value.trim();
  isSearching.value = query.length > 0;

  posts.value = [];
  page.value = 0;
  allLoaded.value = false;

  if (!isSearching.value) {
    await loadMorePosts();
  } else {
    loading.value = true;
    const searchedPosts = await getPosts(20, 0, query);
    posts.value = searchedPosts;
    loading.value = false;
    if (searchedPosts.length < 20) {
      allLoaded.value = true;
    }
  }
};

let debounceTimeout: ReturnType<typeof setTimeout> | null = null;

watch(searchQuery, () => {
  if (debounceTimeout) clearTimeout(debounceTimeout);
  debounceTimeout = setTimeout(() => {
    triggerSearch();
  }, 300);
});

const showNewPost = (payload: { title: string; body: string }) => {
  posts.value.unshift({
    id: Math.floor(Math.random() * 10000),
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
  <div class="w-full flex flex-col items-center justify-center py-12 px-4">
    <div class="w-full min-[420px]:w-[400px]">
      <div class="w-full flex justify-between mb-2 items-baseline">
        <div class="font-bold text-xl">Posts</div>
        <div @click="showAddPost = true" class="text-[#3679FF] text-sm cursor-pointer">Add post</div>
      </div>
      <div
        class="mt-4 mb-4 w-full border border-gray-400 rounded-xl px-2 py-1 text-gray-800 flex focus-within:border-orange-400 focus-within:ring-1 focus-within:ring-orange-300"
      >
        <img src="../assets/search.svg" class="mr-1.5" />
        <input v-model="searchQuery" type="text" class="w-full focus:outline-none" />
        <img
          @click="
            searchQuery = '';
            triggerSearch();
          "
          class="cursor-pointer ml-1.5"
          src="../assets/clear.svg"
        />
      </div>
      <div class="flex flex-col gap-3">
        <AddPost @newpost="showNewPost" @close="showAddPost = false" v-if="showAddPost" />
        <Post v-for="post in posts" :key="post.id" :post="post" />
        <div v-if="!loading && isSearching && posts.length === 0" class="text-center text-gray-500 mt-4">
          No results found
        </div>
      </div>
    </div>
  </div>
</template>
