<template>
  <div v-if="modelValue" class="fixed top-0 left-0 bg-gray-200 w-full h-screen">
    <div class="h-4/5 p-6">
      <slot />
    </div>
    <button
      class="absolute bottom-4 p-2 border text-xl"
      @click="$emit('update:modelValue', false)"
    >
      close
    </button>
  </div>
</template>

<script setup lang="ts">
defineEmits(["update:modelValue"]);

const props = defineProps({
  modelValue: Boolean,
});
</script>
