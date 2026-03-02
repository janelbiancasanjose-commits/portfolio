<template>
  <a
    :href="project.url"
    target="_blank"
    rel="noopener noreferrer"
    class="text-decoration-none"
  >
    <div class="card project-card h-100 w-100">
      <img
        :src="project.image"
        :alt="project.title"
        class="img-fluid"
      />
      <h5 class="mb-3 text-center" id="project-title">{{ project.title }}</h5>
    </div>
  </a>
</template>

<script setup>
defineProps({
  project: {
    type: Object,
    required: true
  }
});
</script>
