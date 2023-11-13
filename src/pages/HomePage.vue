<template>
    <HeroComponent />
    <MainContentComponent :breeds="breeds" :dogs="dogs" />
</template>

<script setup lang="ts">
import HeroComponent from '@/components/HeroComponent.vue';
import MainContentComponent from '@/components/MainContentComponent.vue';
import { fetchBreeds, fetchDogsByBreedAndSubBreed } from '@/queries/index';
import { Breeds, ResponseType } from '@/types';
import { onMounted, ref } from 'vue';

const breedsResponse = ref<ResponseType>();
const breeds = ref<Breeds>({});

const dogsResponse = ref<ResponseType>();
const dogs = ref<string[]>([]);

onMounted(async () => {
    breedsResponse.value = await fetchBreeds();
    breeds.value = breedsResponse.value?.message as Breeds;

    dogsResponse.value = await fetchDogsByBreedAndSubBreed(Object.keys(breeds.value)[0],
        "All");
    dogs.value = dogsResponse.value?.message as string[];
})
</script>