<script setup>
import { ref, computed, onMounted } from 'vue';

const props = defineProps({
    initialDate: { type: String, default: '' },
});

const emit = defineEmits(['date-selected']);

onMounted(() => {
    emit('date-selected', formatYMD(selectedDate.value));
});

const months = [
    'Январь',
    'Февраль',
    'Март',
    'Апрель',
    'Май',
    'Июнь',
    'Июль',
    'Август',
    'Сентябрь',
    'Октябрь',
    'Ноябрь',
    'Декабрь',
];

const weekdays = ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'];

// UTILS
function parseYMD(str) {
    if (!str) {
        return null;
    }

    const m = /^(\d{4})-(\d{2})-(\d{2})$/.exec(str);
    if (!m) {
        return null;
    }

    const y = Number(m[1]),
        mo = Number(m[2]) - 1,
        d = Number(m[3]);

    return new Date(y, mo, d);
}

function formatYMD(date) {
    const y = date.getFullYear();
    const m = String(date.getMonth() + 1).padStart(2, '0');
    const d = String(date.getDate()).padStart(2, '0');
    return `${y}-${m}-${d}`;
}

const today = new Date();
const initial = parseYMD(props.initialDate) || today;

const currentYear = ref(initial.getFullYear());
const currentMonth = ref(initial.getMonth());
const selectedDate = ref(initial);

const currentMonthName = computed(() => months[currentMonth.value]);

const days = computed(() => {
    const firstDay = new Date(currentYear.value, currentMonth.value, 1);
    const lastDay = new Date(currentYear.value, currentMonth.value + 1, 0);

    const totalDays = lastDay.getDate();
    let startDay = firstDay.getDay();
    if (startDay === 0) startDay = 7;

    const daysArray = [];
    for (let i = 1; i < startDay; i++) {
        daysArray.push(null);
    }

    for (let i = 1; i <= totalDays; i++) {
        daysArray.push(i);
    }

    return daysArray;
});

function prevMonth() {
    if (currentMonth.value === 0) {
        currentMonth.value = 11;
        currentYear.value--;
    } else {
        currentMonth.value--;
    }
}

function nextMonth() {
    if (currentMonth.value === 11) {
        currentMonth.value = 0;
        currentYear.value++;
    } else {
        currentMonth.value++;
    }
}

function selectDate(day) {
    selectedDate.value = new Date(currentYear.value, currentMonth.value, day);
    emit('date-selected', formatYMD(selectedDate.value));
}

function isSelected(day) {
    if (!day) return false;
    return (
        selectedDate.value.getFullYear() === currentYear.value &&
        selectedDate.value.getMonth() === currentMonth.value &&
        selectedDate.value.getDate() === day
    );
}
</script>

<template>
    <div class="calendar">
        <div class="calendar-header">
            <button @click="prevMonth">&lt;</button>
            <span>{{ currentMonthName }} {{ currentYear }}</span>
            <button @click="nextMonth">&gt;</button>
        </div>

        <div class="calendar-weekdays">
            <span v-for="day in weekdays" :key="day">{{ day }}</span>
        </div>

        <div class="calendar-days">
            <span
                v-for="(day, index) in days"
                :key="index"
                :class="{ empty: !day, selected: isSelected(day) }"
                @click="day && selectDate(day)"
            >
                {{ day }}
            </span>
        </div>
    </div>
</template>

<style scoped>
.calendar {
    width: 20rem;
    border: 0.125rem solid #ccc;
    padding: 1rem;
    font-family: sans-serif;
    border-radius: 0.25rem;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 1rem;
}

.calendar-header > button {
    padding: 0.75rem 1rem;
    border: 0.125rem solid #ccc;
    border-radius: 0.25rem;
    background: #fff;
    cursor: pointer;
    transition: border 0.2s;
}

.calendar-header > button:hover {
    border: 0.125rem solid rgba(66, 185, 131, 1);
}

.calendar-weekdays,
.calendar-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    text-align: center;
    gap: 0.25rem;
}

.calendar-days > span {
    padding: 0.5rem;
    cursor: pointer;
    border-radius: 0.25rem;
    transition: background-color 0.2s, color 0.2s;
}

.calendar-days > span:hover {
    background-color: rgba(66, 185, 131, 0.1);
}

.calendar-days > span.selected {
    background-color: rgb(66, 185, 131);
    color: white;
}

.calendar-days > span.empty {
    cursor: default;
    background: none;
}
</style>
