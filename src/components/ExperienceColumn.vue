<template>
  <div>
    <h3 class="color-light">{{ title }}</h3>
    <div class="resume-item" v-for="(post, index) in posts" :key="index">
      <div class="year color-darker">{{ post.duration }}</div>
      <div class="resume-title">
        <strong class="color-light" v-html="post.title"></strong>
        <br>
        <strong class="color-light" v-html="post.subtitle"></strong>
      </div>
      <div v-if="post.location" class="location">
        <i class="fa fa-map-pin fa-lg"></i> <span class="color-darker" v-html="post.location"></span>
      </div>
    </div>
  </div>
</template>

<script>
    import fontawesome from "@fortawesome/fontawesome";
    import { fas } from "@fortawesome/free-solid-svg-icons";
    fontawesome.library.add(fas);

    export default {
        name: "ExperienceColumn",
        props: ["posts", "title"],
    };
</script>

<style scoped lang="scss">
@import "@/styles/constants.scss";

.fa-map-pin {
  color: white;
  display:inline; 
}

.location {
  align-items: center;
  font-size: 1.3rem;
}

.resume-item {
  margin-bottom: 30px;
  p {
    font-size: 1.3rem;
  }
  .resume-title {
    font-size: 1.5rem;
    margin-bottom: 5px;
  }
  .year {
    font-weight: 600;
    margin-bottom: 5px;
  }
}
.color-light {
  color: map-get($colors, light);
}
.color-darker {
  color: map-get($colors, dark);
}
</style>
