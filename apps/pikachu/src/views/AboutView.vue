<script setup>
import { helloWorld } from "@pokemon/lib";
</script>

<template>
  <div class="about">
    <h1>This is an about page</h1>
    <h3>{{ helloWorld() }}</h3>
  </div>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
}
</style>
