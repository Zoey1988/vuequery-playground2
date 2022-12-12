<template>
  <div class="container mx-auto p-6 bg-white">
    <div v-if="isLoading">Getting post...</div>
    <div v-else-if="isError">Sorry, try later...</div>
    <div v-else-if="post">
      <div class="font-bold text-xl">{{ post.title }}</div>
      <div class="my-2">
        New title:
        <input type="text" v-model="newTitle" class="border p-2" />

        <!-- TODO-3 mutate title of the post by clicking button -->
        <button class="border p-2">change title</button>
      </div>
      <div class="my-2">{{ post.body }}</div>
    </div>
    <hr class="my-4" />
    <h2 class="font-bold tex-xl">Comments:</h2>
    <div v-for="comment in postComments" class="w-full pb-6">
      <div>
        {{ comment.body }}
      </div>
      <div>Author: {{ comment.name }}</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { useRoute } from "vue-router";
import { useQuery } from "@tanstack/vue-query";

import { api } from "../helpers/api";

import { Comment, Post } from "../types/Post";

const route = useRoute();
const postId = route.params.id;

const newTitle = ref("");

const {
  data: post,
  isError,
  isLoading,
} = useQuery({
  queryKey: ["post", postId],
  queryFn: getPost,
  staleTime: Infinity,
});

function getPost() {
  return api.get<Post>(`/posts/${postId}`).then(({ data }) => data);
}

const { data: postComments } = useQuery({
  queryKey: ["post-comments", postId],
  queryFn: getComments,
  staleTime: 60000,
});

function getComments() {
  return api
    .get<Comment[]>(`posts/${postId}/comments`)
    .then(({ data }) => data);
}
</script>
