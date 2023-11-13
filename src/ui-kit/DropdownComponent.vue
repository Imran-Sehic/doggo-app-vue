<template>
    <div>
        <p class="dropdownHeading">
            {{ type === DropdownType.BREED ? "Breed" : "Sub-Breed" }}
        </p>
        <div ref="dropdownRef">
            <div class="selectedOption" @click="async () => {
                await setClosingAnimation();
                expanded = !expanded;
            }">
                {{ option }}
            </div>
            <div v-if="expanded" class="options" :class="{ closing: closing }">
                <div v-for="(item, index) in list" :key="index" class="option" @click="async () => {
                    //emit('update:option', type, item)
                    option = item
                    await setClosingAnimation();
                    expanded = false;
                }">
                    {{ item }}
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import { DropdownType } from '@/types';
import { MaybeElement, onClickOutside } from '@vueuse/core';
import { ref } from 'vue';
defineProps<{
    list: string[],
    type: DropdownType,
}>();

const emit = defineEmits<{
    (e: 'update:option', dropdownType: DropdownType, option: string): void
}>();

const { option } = defineModels<{
    option: string
}>()

const expanded = ref<boolean>(false);
const closing = ref<boolean>(false);
const dropdownRef = ref<MaybeElement>();

onClickOutside(dropdownRef, async () => {
    await setClosingAnimation();
    expanded.value = false;
})

// Setting closing delay to apply fade-out animation before setting expanded to false and unmount dropdown from the DOM
const setClosingAnimation = async () => {
    if (expanded) {
        closing.value = true;
        await new Promise((resolve) => setTimeout(resolve, 200));
        closing.value = false;
    }
};
</script>

<style scoped>
.dropdownHeading {
    font-weight: bold;
    margin-bottom: 8px;
    text-shadow: -2px 0 white, 0 2px white, 2px 0 white, 0 -2px white;
}

.selectedOption {
    border: 2px solid rgb(54, 120, 182);
    border-radius: 5px;
    position: relative;
    padding: 8px 12px;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    width: 200px;
    color: rgb(54, 120, 182);
    font-weight: bold;
    background-color: white;
}

.selectedOption:hover {
    background-color: gainsboro;
    transition: 0.2s all ease-in-out;
}

.options {
    position: absolute;
    border: 2px solid rgb(54, 120, 182);
    overflow-y: auto;
    width: 200px;
    max-height: 300px;
    border-radius: 5px;
    margin-top: 10px;
    background-color: white;
    z-index: 2;
    animation: fade-in 0.2s ease-in-out;
}

@keyframes fade-in {
    0% {
        opacity: 0;
    }

    100% {
        opacity: 1;
    }
}

.closing {
    animation: fade-out 0.2s forwards ease-in-out;
}

@keyframes fade-out {
    0% {
        opacity: 1;
    }

    100% {
        opacity: 0;
    }
}

.options::-webkit-scrollbar {
    width: 7px;
}

.options::-webkit-scrollbar-thumb {
    background: rgb(102, 163, 219);
    border-radius: 5px;
}

.options::-webkit-scrollbar-thumb:hover {
    background: rgb(54, 120, 182);
}

.option {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 8px 12px;
    border-radius: 5px;
    cursor: pointer;
    color: rgb(54, 120, 182);
}

.option:hover {
    background-color: rgb(54, 120, 182);
    color: white;
    transition: 0.1s all ease-in-out;
}
</style>