<template>
  <div id="main">
    <h1>🇨🇦 Air Canada Flight Status</h1>
    <NuxtPage />
  </div>
</template>
<style>
.image {
  max-width: 100%;
}

video {
  max-width: 100%;
}
#main {
  max-width: 840px;
  margin-right: auto;
  margin-left: auto;
}
body {
    font-family: "Inter", sans-serif;
    background-color: #2e3440;
    color: white;
  }
</style>
<script setup>
useHead({
  title: "🇨🇦 Air Canada Flight Status",
  script: [

  ],
  link: [
      {
        href: "https://fonts.googleapis.com/css2?family=Inter:wght@300&display=swap",
        rel: "stylesheet"
      }
  ],
});
</script>
