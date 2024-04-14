<template>
  <div>
    <Suspense>
      <AsyncCityView />
      <template #fallback>
        <div class="flex justify-center items-center mt-6">
          <div
            class="animate-spin rounded-full h-32 w-32 border-b-2 border-white"
          ></div>
        </div>
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AsyncCityView from "./../components/AsyncCityView.vue";
</script>
