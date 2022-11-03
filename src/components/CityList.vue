<template>
    <div>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const savedCities = ref({});
const getCities = () => {
    if(localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
    };

    const requests = {};
    savedCities.value.forEach((city) => {
        requests.push(
            axios.get(``);
        );
    });
}
</script>