<template>
    Hello World! <br>
    Welcome to Laravel VILT Stack Development <br>
    <hr>
    Reactive state for counter check: {{ counter }}
</template>

<script setup>
import { ref } from 'vue';

const counter = ref(0);

setInterval(() => counter.value++, 1000);
</script>
