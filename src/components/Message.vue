<template>
    <div class="message" v-if="message"> {{ message }}</div>
</template>

<script setup lang="ts">
    defineProps({
        message: String
    })
</script>

<style>
    .message {
        font-size: 18px;
        margin: 20px;
        color: #2c3e50;
        font-weight: bold;
        min-height: 27px;
    }
</style>