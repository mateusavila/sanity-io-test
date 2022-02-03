<script setup lang="ts">
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
import { reactive } from 'vue'
import sanity from './sanity.js'
import imageUrlBuilder from '@sanity/image-url'
import blockContent from '@sanity/block-content-to-html'
const h = blockContent.h
const imageBuilder = imageUrlBuilder(sanity)
const store = reactive({
  posts: null
})

sanity.fetch(`*[_type == "post"]{
  _id,
  title,
  slug,
  body,
  mainImage{
    asset->{
      _id,
      url
    }
  }
}`).then((response) => {
  
  const map = response.map((item) => {
    return {
      ...item,
      block: blockContent({
        blocks: item.body,
        dataset: sanity.clientConfig.dataset,
        projectId: sanity.clientConfig.projectId
      })
    }
  })
  store.posts = map
}).catch((error) => console.log(error))
</script>

<template>
  <header>
    <div class="container">
      <h1>Testing sanity.io</h1>
    </div>
  </header>
  <main>
    <div class="table-box">
      <div class="container">
        <h2>Blog posts</h2>
        <div class="posts-list">
          <div class="posts-item" v-for="(post, index) in store.posts" :key="index">
            <h2>{{post.title}}</h2>
            <img class="fit-image" :src="post.mainImage.asset.url" :alt="post.title" width="300" height="200" loading="lazy">
            <div class="text" v-html="post.block"></div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style>
  html, body {
    padding: 0;
    margin: 0;
    font-family: Arial, sans-serif;
  }
  *, *:before, *:after {
    box-sizing: border-box;
  }
  .container {
    max-width: calc(100% - 40px);
    width: 1200px;
    margin: 0 auto;
  }
  header {
    background: #000;
    height: 80px;
  }
  header .container {
    height: 100%;
    display: flex;
    align-items: center;
  }
  header h1 {
    color: #fff;
  }
  main {
    margin: 50px 0;
  }
  .fit-image{
    object-fit: cover;
  }
</style>
