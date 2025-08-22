<script setup>
import Calendar from './components/Calendar.vue';
import { ref } from 'vue';

const selected = ref('');

function onDateSelected(date) {
    selected.value = date;
}
</script>

<template>
    <div>
        <Calendar initialDate="2025-08-22" @date-selected="onDateSelected" />
        <p>Выбранная дата: {{ selected }}</p>
    </div>
</template>
