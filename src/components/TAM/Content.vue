<template>
  <section class="media">
    <RouterLink to="" class="media-title">
      {{ type == "movie" ? "Фильмы" : "Сериалы" }}
      <img src="@/assets/images/arrow.svg" alt="" />
    </RouterLink>
    <Swiper 
        :modules="modules"
        navigation
        class="media-slider"
        :breakpoints="swiperOptions.breakpoints"
        spaceBetween="25"
    >
        <SwiperSlide class="media-slider-item" v-for="item in content" :key="item.id" @click="getItem(item)">
            <img v-lazy="imgMini + item.poster_path" alt="">
        </SwiperSlide>
        <SwiperSlide class="media-slider-item">
            <RouterLink :to="type == 'movie' ? '/movies' : '/tvs'">
                <img src="@/assets/images/arrow.svg" alt=""> Все {{type == 'movie' ? 'Фильмы' : 'Сериалы'}}
            </RouterLink>
        </SwiperSlide>
    </Swiper>
    <ItemBlock :active="active" @close="active = false" :media="media" :type="type" />
  </section>
</template>
<script setup>
import ItemBlock from '@/components/ItemBlock/ItemBlock.vue'
import { imgMini } from "@/url";
import { Swiper, SwiperSlide } from "swiper/vue";
import { Navigation } from 'swiper/modules';
import { ref, computed } from 'vue';
import "swiper/scss";
import 'swiper/scss/navigation';
import { usePopular } from '@/store/popular'
const popular = usePopular()
import { useGetItemById } from '@/store/getItemById'
const getItemById = useGetItemById()
import { useActors } from '@/store/actors'
const actorsStore = useActors()
popular.getPopular(props.type)
const props = defineProps(["type"]);
const modules = ref([Navigation])
const content = computed(() => props.type == 'movie' ? popular.popularMovies : popular.popularTvs)
let active = ref(false)
let media = ref(null)
const swiperOptions = ref({
  breakpoints: {
    1660: {
      slidesPerView: 5.5
    },
    1160: {
      slidesPerView: 4
    },
    876: {
      slidesPerView: 3
    },
    578: {
      slidesPerView: 1.5
    }
  }
})
const getItem = async (item) => {
  active.value = true
  await getItemById.getItemById({type: props.type, id: item.id})
  media.value = props.type == 'movie' ? getItemById.movie : getItemById.tv 
  await actorsStore.getActors({type: props.type, id: item.id, count: 4})
}
</script>
