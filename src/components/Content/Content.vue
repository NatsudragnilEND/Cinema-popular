<template>
<section class="container content">
    <h2>Все {{type=='movie' ? 'фильмы' : 'сериалы'}}</h2>
    <div class="content-cards">
        <RouterLink to="" v-for="item in content" :key="item.id">
            <img :src="imgMini + item.poster_path" alt="">
            <h3>{{item.title || item.name}}</h3>
        </RouterLink>
    </div>
    <div class="pagination">
        <button @click="changePage('prev')" :disabled="currentPage === 1">PREV</button>
        <span>{{currentPage}} - {{maxPage}}</span>
        <button @click="changePage('next')" :disabled="currentPage === maxPage">NEXT</button>
    </div>
</section>
</template>

<script setup>
const props = defineProps(['type'])
import { imgMini } from "@/url";
import { ref, computed, onMounted } from 'vue';
import { usePopular } from '@/store/popular'
const popular = usePopular()
const currentPage = ref(1)
const maxPage = ref(500)
const content = ref([])
onMounted(async () => {
    await popular.getPopular(props.type, currentPage.value)
    content.value = props.type == 'movie' ? popular.popularMovies : popular.popularTvs
})
const changePage = async (dir) => {
    if(dir === 'next') currentPage.value++
    else currentPage.value--
    await popular.getPopular(props.type, currentPage.value)
    content.value = props.type == 'movie' ? popular.popularMovies : popular.popularTvs
    window.scroll(0, 0)
} 

</script>
