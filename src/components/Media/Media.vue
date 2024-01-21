<template>
  <div class="media-info active current" v-if="media">
    <div class="media-info-content">
      <div class="media-info-content-block">
        <h2>{{ media.title || media.name }}</h2>
        <p>{{ media.overview || "Неописуемый..." }}</p>
        <p class="media-info-content-block-descr">
          <span>{{
            new Date(media.release_date || media.first_air_date).getFullYear()
          }}</span>
          <span>{{ getGenres }}</span>
          <span
            >{{
              type == "movie"
                ? `${Math.floor(media.runtime / 60)}h ${media.runtime % 60}m`
                : `${media.number_of_seasons}s ${media.number_of_episodes}e`
            }}
          </span>
        </p>
        <a
          :href="'https://www.youtube.com/watch/' + trailer"
          class="btn-more"
          target="blank"
          v-if="trailer"
          >Смотреть трейлер</a
        >
        <Actors :actors="getActors" />
      </div>
      <img v-lazy="imgFull + media.backdrop_path" alt="" />
    </div>
  </div>
  <ul class="container media-descr" v-if="media">
    <li>
      <h3>Бюджет</h3>
      <p v-if="media.budget">{{'$'+media.budget}}</p>
      <p v-else>{{'Неизвестно'}}</p>
    </li>
    <li>
      <h3>Сборы</h3>
      <p v-if="media.revenue">{{'$'+media.revenue}}</p>
      <p v-else>{{'Неизвестно'}}</p>
    </li>
    <li>
      <h3>Статус</h3>
      <p>{{media.status || 'Неизвестно'}}</p>
    </li>
    <li>
      <h3>Исходное название</h3>
      <p>{{media.original_title || 'Неизвестно'}}</p>
    </li>
  </ul>
  <section class="container rec">
      <h2>Рекомендации</h2>
      <div class="rec-content">
          <RouterLink :to="`/${type}/${item.id}`" v-for="item in rec" :key="item.id" @click="changeContent(item.id)">
              <img :src="imgMini + item.poster_path" alt="">
          </RouterLink>
      </div>
  </section>
  
</template>
<script setup>
const props = defineProps(["type"]);
import { imgMini, imgFull } from "@/url";
import { ref, computed, onMounted } from "vue";
import { useGetItemById } from "@/store/getItemById";
const getItemById = useGetItemById();
import { useActors } from "@/store/actors";
const actorsStore = useActors();
import { useTrailer } from "@/store/trailer";
const trailerStore = useTrailer();
import { useRec } from "@/store/rec";
const recStore = useRec();
let media = ref(null);
let actors = ref(null);
let trailer = ref(null);
let rec = ref(null);

import { useRoute } from "vue-router";
onMounted(async () => {
  await getItemById.getItemById({ type: props.type, id: useRoute().params.id });
  media.value = props.type == "movie" ? getItemById.movie : getItemById.tv;
  await actorsStore.getActors({
    type: props.type,
    id: media.value.id,
    count: 6,
  });
  actors.value =
    props.type == "movie" ? actorsStore.actorsMovie : actorsStore.actorsTv;
  await trailerStore.getTrailer({ type: props.type, id: media.value.id });
  trailer.value =
    props.type == "movie" ? trailerStore.movieTrailer : trailerStore.trailerTv;
  await recStore.getRec(props.type,media.value.id);
  rec.value =
    props.type == "movie" ? recStore.recMovies : recStore.recTvs;
    
});
const getActors = computed(() =>
  props.type == "movie" ? actorsStore.actorsMovie : actorsStore.actorsTv
);
const getGenres = computed(() =>
  media.value.genres.reduce((acc, item) => acc + `, ${item.name}`, "")
);
const changeContent = async (id) => {
  await getItemById.getItemById({ type: props.type, id: id });
  window.location.reload()
}
</script>