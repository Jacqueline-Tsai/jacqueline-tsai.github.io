<template>
  <transition name="fade" tag="div" class="wrapper" mode="out-in">
    <div class="wrapper" id="app">
      <LandingPage :user="findSlug('profile')" />
      <Description :user="findSlug('profile')" :content="findSlug('description')" :links="findSlug('links')" />
      <Experience :content="findSlug('experiences')" />
      <Skills :content="findSlug('skills')" />
      <Projects :content="findSlug('projects')" />
      <Footer :user="findSlug('profile')" :links="findSlug('links')" />
    </div>
  </transition>
</template>

<script>
import LandingPage from "./components/LandingPage.vue";
import Description from "./components/Description.vue";
import Experience from "./components/Experience.vue";
import Skills from "./components/Skills.vue";
import Projects from "./components/Projects.vue";
import Footer from "./components/Footer.vue";

import data from '../data/data.json';

export default {
  name: "App",
  components: {
    LandingPage,
    Description,
    Experience,
    Skills,
    Projects,
    Footer,
  },
  data: () => ({
    user: {},
    posts: [],
  }),
  methods: {
    findSlug(slug) {
      return data[slug];
    },
  },
  created() {
  },
};
</script>

<style scoped lang="scss">
@import "@/styles/constants.scss";

#app {
  font-family: Montserrat-Regular, serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.wrapper {
  height: 100%;
}
</style>
