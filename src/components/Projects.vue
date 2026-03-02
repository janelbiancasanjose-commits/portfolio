<template>
  <div id="projects" class="container-fluid py-5">
    <h2 class="text-center mb-5">MY PROJECTS</h2>

    <div class="row g-4">
      <div
        v-for="project in projects"
        :key="project.id"
        class="col-sm-12 col-md-4"
      >
        <ProjectCard :project="project" />
      </div>
    </div>
  </div>
</template>

<script setup>
import ProjectCard from './ProjectCard.vue';
import projects from '../data/projects.json';
</script>
