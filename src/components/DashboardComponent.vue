<template>
    <div class="dashboard">
        <h2 class="heading">
            Search by <span>breed</span> and <span>sub-breed</span>
        </h2>
        <div class="ctaSection">
            <div class="dropdowns">
                <DropdownComponent :list="Object.keys(breeds)" :type="DropdownType.BREED" v-model:option="breed" />
                <DropdownComponent :list="['All', ...(Object.keys(breeds).length > 0 ? breeds[breed] : [])]"
                    :type="DropdownType.SUB_BREED" v-model:option="subBreed" />
            </div>
            <ButtonComponent content="Search" @click="onSearch" />
        </div>
    </div>
</template>

<script setup lang="ts">
import { fetchDogsByBreedAndSubBreed } from '@/queries';
import { Breeds, DropdownType, ListingText } from '@/types';
import ButtonComponent from '@/ui-kit/ButtonComponent.vue';
import DropdownComponent from '@/ui-kit/DropdownComponent.vue';
import { ref, watch } from 'vue';

const props = defineProps<{
    breeds: Breeds
}>();

const emit = defineEmits<{
    (e: 'update:loading', loading: boolean): void
    (e: 'update:dogs', images: string[]): void
    (e: 'update:listing-text', text: ListingText): void
}>();

const breed = ref<string>("");
const subBreed = ref<string>("");

watch(breed, () => {
    subBreed.value = "All"
})

watch(() => props.breeds, (breeds) => {
    breed.value = Object.keys(breeds)[0];
    subBreed.value = breeds[breed.value].length > 0 ? breeds[breed.value][0] : "All"
})

const onSearch = async () => {
    emit('update:loading', true);
    const dogsResponse = await fetchDogsByBreedAndSubBreed(breed.value, subBreed.value);
    emit('update:dogs', dogsResponse.message as string[]);
    emit('update:loading', false);
    emit('update:listing-text', { breed: breed.value, subBreed: subBreed.value });
}
</script>

<style scoped>
.dashboard {
    background-image: url('/dashboard-background.jpg');
    background-repeat: no-repeat;
    background-position: right;
    background-size: contain;
    padding: 5rem;
    margin-bottom: 4rem;
    border: 3px solid rgb(54, 120, 182);
    box-shadow: 10px 10px lightblue;
}

.heading {
    margin-bottom: 16px;
    text-shadow: -3px 0 white, 0 3px white, 3px 0 white, 0 -3px white;
}

.heading>span {
    color: rgb(54, 120, 182);
}

.ctaSection {
    display: flex;
    align-items: end;
    justify-content: space-between;
}

.dropdowns {
    display: flex;
    gap: 40px
}

/*smaller desktop*/
@media screen and (max-width: 1150px) {
    .wrapper {
        padding: 3rem
    }
}

/*bigger tablet*/
@media screen and (max-width: 950px) {
    .ctaSection {
        align-items: start;
        flex-direction: column;
    }

    .dropdowns {
        margin-bottom: 3rem;
    }
}

/*tablet*/
@media screen and (max-width: 750px) {
    .dropdowns {
        margin-bottom: 3rem;
        flex-direction: column;
    }
}

/*smaller tablet*/
@media screen and (max-width: 600px) {
    .ctaSection {
        align-items: center;
    }
}
</style>