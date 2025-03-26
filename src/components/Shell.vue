<template>
    <div
        class="shell"
        :class="{
            'active': shell.active,
            'winner': showResult && index === pearlPosition,
            'selected': selectedShell === index,
            'animated': isShuffling,
            'merging': isMerging,
            'fade-out': fadeOutShell,
            'disable': gameState !== 'guessing'
        }"
        :style="getShellStyle()"
        @click="handleClick"
    >
        <transition name="fade">
            <img src="../assets/shell.svg" class="shell-svg" :class="{ 'transparent': fadeOutShell === index }" />
        </transition>

        <svg
            v-if="(showPearl && index === initialPosition && !showResult) || (showResult && showPearl && index === pearlPosition)"
            class="pearl-svg"
            viewBox="0 0 20 20"
            width="30"
            height="30"
        >
            <circle cx="10" cy="10" r="6" fill= "#2c3e50" />
        </svg>
    </div>
</template>

<script setup lang="ts">
    const props = defineProps({
        shell: {
            type: Object,
            required: true,
        },
        index: Number,
        isShuffling: Boolean,
        showResult: Boolean,
        pearlPosition: Number,
        selectedShell: Number,
        showPearl: Boolean,
        initialPosition: Number,
        gameState: String,
        isMerging: Boolean,
        fadeOutShell: Number,
    });

    const emit = defineEmits(["select"]);

    const handleClick = () => {
        if (props.gameState !== 'guessing') return;
        setTimeout(() => {
            emit("select");
        });
    }

    const getShellStyle = () => {
        return {
            transform: props.shell.transform,
            cursor: props.gameState === 'guessing' ? 'pointer' : 'default'
        };
    }
</script>

<style>
    .shell {
        position: relative;
        transition: transform 0.5s ease-in-out;
        will-change: transform;
    }

    .shell-svg {
        width: 80px;
        height: 80px;
        transition: opacity 0.5s ease-in-out, transform 0.3s ease;
    }

    .shell-svg.transparent {
        opacity: 0.2;
    }

    .shell:hover .shell-svg {
        transform: translateY(-5px);
    }

    .shell.active .shell-svg {
        transform: translateY(-10px);
    }

    .shell.selected .shell-svg {
        transform: translateY(-15px) scale(1.05);
    }

    .shell.animated {
        transition: transform 0.2s ease-in-out;
    }

    .shell.merging {
        transition: transform 0.8s cubic-bezier(0.68, -0.55, 0.27, 1.55);
    }

    .pearl-svg {
        position: absolute;
        bottom: 10px;
        left: 50%;
        transform: translateX(-50%);
        width: 60px;
        height: 60px;
        z-index: 0;
        transition: opacity 0.5s ease-out;
    }

    .pearl-fade-enter-active,
    .pearl-fade-leave-active {
        transition: opacity 0.5s ease;
    }

    .pearl-fade-enter-from,
    .pearl-fade-leave-to {
        opacity: 0;
    }

    .fade-enter-active,
    .fade-leave-active {
        transition: opacity 0.5s;
    }

    .fade-enter-from,
    .fade-leave-to {
        opacity: 0;
    }

    .shell-top {
        position: absolute;
        bottom: 0;
        width: 100px;
        height: 70px;
        background-color: #d2b48c;
        border-radius: 50% 50% 0 0;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
        z-index: 2;
    }

    .shell-top::before {
        content: '';
        position: absolute;
        top: -10px;
        left: 50%;
        transform: translateX(-50%);
        width: 20px;
        height: 20px;
        background-color: #d2b48c;
        border-radius: 50%;
    }

    .pearl {
        color: #2c3e50;
        position: absolute;
        bottom: 5px;
        left: 50%;
        transform: translateX(-50%);
        width: 20px;
        height: 20px;
        background: radial-gradient(circle at 30% 30%, #ffffff, #f0f0f0);
        border-radius: 50%;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
        z-index: 0;
        transition: opacity 0.5s ease-out;
    }

    .shell.winner .shell-top {
        background-color: #ffd700;
    }

</style>