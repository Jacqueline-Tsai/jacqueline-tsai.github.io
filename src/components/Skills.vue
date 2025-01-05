<template>
    <section id="skills">
        <AnimateOnVisible name="fadeDown" :duration="1">
            <Title :title="content.title" :description="content.description" />
        </AnimateOnVisible>

        <AnimateOnVisible name="fadeRight" :duration="1">
            <div class="section-content">
                <div class="container-fluid">
                    <div class="skill-sets">
                        <div v-for="(set, index) in content.sets" :key="index" class="skill-set">
                            <h2 class="skill-set-title">{{ set.title }}</h2>
                            <ul class="skill-items">
                            <li v-for="(item, itemIndex) in set.items" :key="itemIndex" class="skill-item">
                                {{ item }}
                            </li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </AnimateOnVisible>
        
    </section>
</template>

<script>
    import Title from './Title.vue';

    export default {
        name: 'Skills',
        components: {
            Title,
        },
        props: ['content'],
        methods: {
            getImgUrl(img) {
                return require('../assets/img/logo/'+img);
            },
        },
    }
</script>

<style>
    .skill-section {
        margin: 2rem 0;
    }

    .section-title {
        font-weight: bold;
        margin-bottom: 0.5rem;
    }

    .section-description {
        color: #666;
        margin-bottom: 1rem;
    }

    .skill-sets {
        margin-left: 15%;
        margin-right: 15%;
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        gap: 1.5rem;
    }

    .skill-set {
        border: 1px solid #e0e0e0;
        border-radius: 8px;
        padding: 1rem;
        box-sizing: border-box;
        text-align: center;
    }

    .skill-set-title {
        font-weight: bold;
        margin: 0.75rem;
        margin-bottom: 1.5rem;
    }

    .skill-items {
        list-style-type: none;
        padding: 0;
        margin: 0;
        display: flex;
        flex-wrap: wrap;
        gap: 0.5rem;
        justify-content: flex-start;
    }

    .skill-item {
        display: inline-flex;
        align-items: center;
        background-color: #f5f5f5;
        border-radius: 4px;
        padding: 0.5rem 1rem;
        color: #333;
        box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
        text-align: center;
        white-space: nowrap;
    }
</style>