<template>
  <section class="top">
    <h2 class="top-title">
      ТОП
      <span>10</span>
    </h2>
    <Swiper 
        :modules="modules"
        navigation
        class="top-slider"
        spaceBetween="23"
        :breakpoints="swiperOptions.breakpoints"
    >
      <SwiperSlide class="top-slider-item" v-for="(item,idx) in top.top" :key="item.id">
        <router-link :to="'movie/'+item.id" class="main-upcoming-item-next top-slider-item-content">
          <img :src="imgMini + item.poster_path" alt="" />
          <div><span>{{idx + 1}}</span></div>
        </router-link>
      </SwiperSlide>
    </Swiper>
  </section>
</template>
<script setup>
import { imgMini } from "@/url";
import { Navigation } from "swiper/modules";
import { Swiper, SwiperSlide } from "swiper/vue";
import { ref, computed } from "vue";
import "swiper/scss";
import "swiper/scss/navigation";
const modules = ref([Navigation]);
const swiperOptions = ref({
  breakpoints: {
    320: { slidesPerView: 1 },
    570: { slidesPerView: 1.5 },
    900: { slidesPerView: 3 },
    1140: { slidesPerView: 3 },
    1450: { slidesPerView: 3.3 }
  },
});
import { useTop } from "@/store/top";
const top = useTop()
top.getTop()
</script>
