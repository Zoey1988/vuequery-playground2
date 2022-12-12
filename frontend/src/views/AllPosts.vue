<template>
  <div class="container mx-auto p-6">
    <h1 class="font-bold text-2xl mb-4">All Posts</h1>
    <div v-if="allPostsLoading">Getting posts...</div>
    <div v-else-if="allPostsError">Sorry, try later...</div>
    <div v-else class="flex flex-wrap">
      <div
        v-for="post in allPosts"
        class="border p-4 mr-2 mb-3 basis-1/3 grow rounded-lg bg-white"
      >
        <router-link
          :to="{ name: 'posts-id', params: { id: post.id } }"
          class="font-bold text-xl hover:underline"
        >
          {{ post.title }}
        </router-link>

        <div class="flex justify-between mt-4">
          <span>userId: {{ post.userId }}</span>
          <button class="text-orange-400" @click="() => showComments(post.id)">
            comments
          </button>
        </div>
      </div>
    </div>
    <div>
      <!-- TODO-2 refech data by clicking button -->
      <button>update me</button>
    </div>
    <base-modal v-model="modalIsOpen">
      <div v-if="postCommentsLoading">Getting comments...</div>
      <div v-else-if="postCommentsError">Sorry, try later...</div>
      <div v-else-if="!postComments?.length">There is no comments</div>
      <div v-for="comment in postComments" class="w-full pb-6">
        <div>
          {{ comment.body }}
        </div>
        <div>Author: {{ comment.name }}</div>
      </div>
    </base-modal>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { RouterLink } from "vue-router";
import { useQuery } from "@tanstack/vue-query";

import BaseModal from "../components/BaseModal.vue";

import { api } from "../helpers/api";

import type { Post, Comment } from "../types/Post";

function getPosts() {
  return api.get<Post[]>("/posts").then(({ data }) => data);
}

const {
  data: allPosts,
  isError: allPostsError,
  isLoading: allPostsLoading,
} = useQuery({
  queryKey: ["all-posts"],
  queryFn: getPosts,
  staleTime: Infinity,
});

const modalIsOpen = ref<boolean>(false);
const selectedPost = ref<number>();

function getComments() {
  return api
    .get<Comment[]>(`posts/${selectedPost.value}/comments`)
    .then(({ data }) => data);
}

// TODO-1 Query for comments, only when needed
const {
  data: postComments,
  isError: postCommentsError,
  isLoading: postCommentsLoading,
} = useQuery({
  queryKey: ["post-comments", selectedPost],
  queryFn: getComments,
  staleTime: 60000,
});

function showComments(id: number) {
  modalIsOpen.value = true;
  selectedPost.value = id;
}
</script>
