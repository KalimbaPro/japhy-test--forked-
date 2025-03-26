<template>
    <div class="score-board">
        <div class="score">
            <span>Victoires: {{ wins }}</span>
            <span>Défaites: {{ looses }}</span>
        </div>
    </div>
</template>

<script setup lang="ts">
    defineProps({
        wins: Number,
        looses: Number,
    })
</script>

<style>
    .score-board {
        display: flex;
        justify-content: center;
        margin-bottom: 40px;
    }

    .score {
        display: flex;
        gap: 20px;
        font-size: 18px;
        color: #2c3e50;
        font-weight: bold;
    }
</style>