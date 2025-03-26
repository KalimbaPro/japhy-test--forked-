<template>
    <button
        class="game-button"
        @click="startGame"
    >
        {{  gameState === 'initial' ? 'Commencer' : 'Rejouer' }}
    </button>
</template>

<script setup>
    defineProps({
        gameState: String,
        isShuffling: Boolean,
        startGame: Function,
    })
</script>