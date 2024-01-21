<template>
<div class="actors" v-if="actors">
    <div class="actors-item" v-for="actor in actors" :key="actor.id">
        <img v-lazy="imgMini + actor.profile_path" alt="">
        <span>{{actor.name}}</span>
    </div>
</div>
</template>
<script setup>
import { imgMini } from "@/url";
const props = defineProps(['actors'])

</script>
<style lang="scss" scoped>
.actors{
    display: flex;
    flex-wrap: wrap;
    max-width:500px;
    width: 100%;
    gap: 15px;
    margin-top: 30px;
    &-item{
        display: flex;
        align-items: center;
        gap: 10px;
        border-radius: 25px;
        border: 1px solid #FFF;
        padding: 2px 15px 2px 2px;
        span{
            font-size: 16px;
            font-weight: 400;
            color: white;
            margin-left: 0;
        }
        img{
            width: 50px;
            height: 50px;
            border-radius: 50%;
            object-fit: cover;
        }
    }
}
</style>