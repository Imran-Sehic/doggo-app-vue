<template>
    <div class="wrapper">
        <DashboardComponent :breeds="breeds" @update:dogs="setDogsImages" @update:listing-text="setListingText"
            @update:loading="setIsLoading" />
        <ListingComponent :images="dogsImages" :listingText="listingText" :isLoading="isLoading" />
    </div>
</template>

<script setup lang="ts">
import ListingComponent from '@/components/ListingComponent.vue';
import { Breeds, ListingText } from '@/types';
import { ref, watch } from 'vue';
import DashboardComponent from './DashboardComponent.vue';

const props = defineProps<
    {
        breeds: Breeds
        dogs: string[]
    }>();

const dogsImages = ref<string[]>(props.dogs);

const listingText = ref<ListingText>({
    breed: Object.keys(props.breeds)[0],
    subBreed: "All"
});

watch(props, (value) => {
    listingText.value.breed = Object.keys(value.breeds)[0];
    dogsImages.value = value.dogs;
})

const isLoading = ref<boolean>(false);

const setDogsImages = (images: string[]) => {
    dogsImages.value = images;
}

const setIsLoading = (loading: boolean) => {
    isLoading.value = loading;
}

const setListingText = (text: ListingText) => {
    listingText.value = text;
}
</script>

<style scoped>
.wrapper {
    margin: 8rem;
}

/*smaller desktop*/
@media screen and (max-width: 1150px) {
    .wrapper {
        margin: 5rem
    }
}

/*smaller tablet*/
@media screen and (max-width: 600px) {
    .wrapper {
        margin: 3rem;
    }
}

/*mobile*/
@media screen and (max-width: 500px) {
    .wrapper {
        margin: 1rem;
    }
}
</style>