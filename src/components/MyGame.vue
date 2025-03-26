<template>
    <div class="shell-game">
        <h1>Le Jeu du Bonneteau</h1>

        <ScoreBoard :wins="wins" :looses="looses" />

        <div class="game-area">
            <div class="shells-containers">
                <Shell
                    v-for="(shell, index) in shells"
                    :key="index"
                    :shell="shell"
                    :index="index"
                    :isShuffling="isShuffling"
                    :showResult="showResult"
                    :pearlPosition="pearlPosition"
                    :selectedShell="selectedShell"
                    :showPearl="showPearl"
                    :initialPosition="initialPosition"
                    :gameState="gameState"
                    :isMerging="isMerging"
                    :fadeOutShell="fadeOutShell"
                    @select="selectShell(index)"
                >
                </Shell>
            </div>

            <Message :message="message" />

            <GameButton :gameState="gameState" :isShuffling="false" :startGame="startGame"/>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { reactive, ref } from 'vue';
import GameButton from './GameButton.vue';
import Message from './Message.vue';
import ScoreBoard from './ScoreBoard.vue';
import Shell from './Shell.vue';

    type GameState = 'initial' | 'showing' | 'shuffling' | 'guessing' | 'result';

    const gameState = ref<GameState>('initial');
    const wins = ref(0);
    const looses = ref(0);
    const message = ref('Trouvez la perle cachée sous un des coquillages!');
    const isShuffling = ref(false);
    const showPearl = ref(true);
    const showResult = ref(false);
    const initialPosition = ref(1);
    const pearlPosition = ref(-1);
    const selectedShell = ref(-1);
    const isMerging = ref(false);
    const fadeOutShell = ref(-1);

    const shells = reactive([
        { active: false, transform: 'translateX(-80px)', isVisible: true, originalPosition: '-80px' },
        { active: false, transform: 'translateX(0px)', isVisible: true, originalPosition: '0px' },
        { active: false, transform: 'translateX(80px)', isVisible: true, originalPosition: '80px' }
    ]);

    const fetchRandomPosition = async (): Promise<number> => {
        try {
            const response = await fetch('https://www.random.org/integers/?num=1&min=0&max=2&col=1&base=10&format=plain&rnd=new');
            const text = await response.text();
            console.log(text);
            return parseInt(text.trim(), 10);
        } catch (error) {
            console.error('Error fetching random position:', error);
            return 0;
        }
    }

    const shuffleShells = async () => {
        isShuffling.value = true;
        showPearl.value = false;

        pearlPosition.value = await fetchRandomPosition();

        const shuffleDuration = 2000;

        setTimeout(() => {
            isShuffling.value = false;
            gameState.value = 'guessing';
            message.value = 'Où est la perle? Cliquez sur un coquillage!';
        }, shuffleDuration);
    }

    const startGame = async () => {
        selectedShell.value = -1;
        showResult.value = false;
        fadeOutShell.value = -1;
        message.value = 'Regardez bien où est la perle...';

        gameState.value = 'showing';
        showPearl.value = true;
        initialPosition.value = 1;

        shells.forEach((shell) => {
            shell.isVisible = true;
        });

        await new Promise(resolve => setTimeout(resolve, 1000));

        showPearl.value = false;

        await new Promise(resolve => setTimeout(resolve, 500));

        isMerging.value = true;

        await new Promise(resolve => setTimeout(resolve, 50));

        showPearl.value = false;

        shells[0].transform = 'translateX(0px)';
        shells[2].transform = 'translateX(0px)';

        await new Promise(resolve => setTimeout(resolve, 800));

        console.log('Moving shells back to original positions');
        shells[0].transform = 'translateX(-80px)';
        shells[2].transform = 'translateX(80px)';

        await new Promise(resolve => setTimeout(resolve, 800));

        setTimeout(() => {
            gameState.value = 'shuffling';
            shuffleShells();
        }, 500);
    }

    const selectShell = (index: number) => {
        if (gameState.value !== 'guessing' || isShuffling.value) return;

        selectedShell.value = index;
        showResult.value = true;

        if (index === pearlPosition.value) {
            wins.value++;
            fadeOutShell.value = index;
            setTimeout(() => {
                showPearl.value = true;
            }, 300);
            message.value = 'Bravo! Vous avez trouvé la boule.'
        } else {
            looses.value++;
            message.value = 'Dommage! La perle était sous un autre coquillage.'
        }

        gameState.value = 'result';

        setTimeout(() => {
            selectedShell.value = -1;
        })
    }
</script>

<style scoped>
    h1 {
        color: #2c3e50;
        margin-bottom: 20px;
    }

    .shell-game {
        font-family: 'Arial', sans-serif;
        max-width: 600px;
        margin: 0 auto;
        padding: 20px;
        text-align: center;
        background-color: #f5f5f5;
        border-radius: 10px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }

    .shells-containers {
        display: flex;
        justify-content: center;
        margin-bottom: 40px;
        min-height: 80px;
    }
</style>