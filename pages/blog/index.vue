<template>
  <ul>
    <li v-for="article in articles" :key="article.slug">
      <NuxtLink :to="`/blog/${article.slug}`">{{ article.title }}</NuxtLink>
    </li>
  </ul>
</template>

<script>
export default {
  async asyncData({ $content, params }) {
    const articles = await $content("articles", params.slug)
      .only(["title", "slug"])
      .sortBy("createdAt", "asc")
      .fetch();

    return {
      articles,
    };
  },
};
</script>
