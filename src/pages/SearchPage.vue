<template>
<section class="container search">
    <input type="text" placeholder="Найти фильм, сериал..." required autofocus v-model="query" @input="search(query)">
    <div class="search-content">
        <RouterLink :to="item.title ? '/movie/'+item.id : '/tv/'+item.id" v-for="item in content" :key="item.id">
            <img v-lazy="imgMini + item.poster_path" alt="">
        </RouterLink>
    </div>
</section>
</template>
<script setup>
import { imgMini } from '@/url'
import { ref, onMounted } from "vue";
const query = ref('')
import { useSearch } from "@/store/search";
const searchStore = useSearch()
const content = ref([])
const search = async (query) => {
    await searchStore.getSearch(query)
    content.value = searchStore.content
}
</script>
<style lang="scss">
    .search{
        padding-top: 30px;
        input{
            width: 100%;
            margin-bottom: 31px;
            padding: 20px;
            color: #141414;
            font-size: 18px;
            font-weight: 700;
            background: white;
            border-radius: 10px;
            &::placeholder{
              color: #141414;
            }
        }
        &-content{
            display: grid;
            gap: 24px;
            grid-template-columns: repeat(4, 1fr);
            a{
                border-radius: 10px;
                img{
                    width: 100%;
                    height: 100%;
                    border-radius: 10px;
                    border: 2px black solid;
                }
            }
        }
    }
</style>
