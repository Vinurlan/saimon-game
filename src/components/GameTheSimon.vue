<template>
    <div class="game">
        <h1 class="game__name">Simon Says</h1>
        <div class="game__container">
            <div class="game__simon">
                <div class="panel"
                    v-for="panel of panels"
                    :key="panel.id"
                    :id="panel.id"
                    :style="{background: panel.color}"
                    @click="playerRepeat"
                ></div>
            </div>
            <div class="game__hud">
                <div class="game__info">
                    <h3>Round: {{ round }}</h3>
                    <button v-if="!play" @click="gameStart">Start</button>
                    <p v-else-if="stageGameSequence" class="game__look">Look</p>
                    <p v-else class="game__repeat">Repeat</p>
                    <p v-if="fall" class="game__fall">You completed {{ fall }} rounds</p>
                </div>
                <div class="game__options">
                    <h3>Game Options:</h3>
                    <select name="game-mode" id="gameMode" v-model="mode.current">
                        <option value="easy">Easy</option>
                        <option value="normal">Normal</option>
                        <option value="hard">Hard</option>
                    </select>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import sounds from '../modules/sounds.module'

export default {
    name: 'GameTheSimon',
    data() {
        return {
            panelCount: 4,
            panels: [],
            colors: ['#D04E4E', '#52D04E', '#4E54D0', '#D04ED0'],
            brightColors: ['#FF0000', '#08FF00', '#000CFF', '#FF00FF'], 
            mode: {
                current: 'easy',
                time: {
                    easy: 1500,
                    normal: 1000,
                    hard: 400
                }
            },
            round: 0,
            currentStep: 0,
            play: false,
            fall: 0,
            stageGameSequence: false,
            gameSequence: [],
            playerSequence: [],
        }
    },
    methods: {
        initSaimon() {
            for (let i = 0; i < this.panelCount; i++) {
                this.panels.push({
                    id: i,
                    color: this.colors[i],
                    soundPath: sounds.tones[i]
                })
            }
        },
        gameStart() {
            this.play = true
            this.fall = 0
            this.roundStart()
        },
        gameEnd() {
            this.play = false
            this.fall = this.round - 1
            this.round = 0
            this.stageGameSequence = false
            this.gameSequence = []
            this.playerSequence = []
        },
        roundStart() {
            if (this.play == false) return
            this.round++
            this.currentStep = 0
            this.stageGameSequence = true
            this.playerSequence = []
            this.addGameSequence()    
            this.initGameStep()
        },
        initGameStep() {
            setTimeout(this.gameStep, this.mode.time[this.mode.current])
        },
        gameStep() {
            if (this.currentStep == this.gameSequence.length) {
                this.stageGameSequence = false
                this.currentStep = 0
                return
            }
            this.activePanel(this.gameSequence[this.currentStep])
            
            this.currentStep++
            setTimeout(this.gameStep, this.mode.time[this.mode.current])

        },

        playerRepeat(event) {
            if (this.stageGameSequence || !this.play) return
            
            this.addPlayerSequence(event.target.id)
            this.activePanel(event.target.id)
            if (this.playerSequence[this.currentStep] != this.gameSequence[this.currentStep]) {
                this.gameEnd()
                return
            }

            this.currentStep++
            if (this.currentStep == this.gameSequence.length) {
                this.roundStart()
            }
        },

        addGameSequence() {          
            this.gameSequence.push(Math.floor(Math.random() * this.panels.length))
        },
        addPlayerSequence(id) {
            this.playerSequence.push(id)
        },
        activePanel(id) {
            new Audio(this.panels[id].soundPath).play()
            this.panels[id].color = this.brightColors[id]
            setTimeout(() => this.deactivePanel(id), (
                this.stageGameSequence ? this.mode.time[this.mode.current] / 2 : 100))
        },
        deactivePanel(id) {
            this.panels[id].color = this.colors[id]
        }
    },
    mounted() {
        this.initSaimon()
    }
}
</script>

<style lang="scss" scoped>

@mixin flex($dir: row, $jc: center, $ai: center) {
    display: flex;
    flex-direction: $dir;
    justify-content: $jc;
    align-items: $ai;
}

.game {
    @include flex(column, flex-start);
    width: 100%;
    height: 100%;
    background-color: rgb(216, 216, 216);

    &__container {
        @include flex();
    }

    &__info button {
        border: none;
        font-size: 14pt;
        width: 60px;
        background: #fff;
        color: #000;
    }
    &__info button:hover {
        width: 100px;
        background: #000;
        color: #fff;
        transition-property: width background;
        transition-duration: 0.3s;
    }

    &__options select {
        width: 100px;
        font-size: 12pt;
    }

    &__simon {
        display: grid;
        gap: 5px;
        grid-template-rows: 1fr 1fr;
        grid-template-columns: 1fr 1fr;

        border-radius: 50%;
    }

    &__simon .panel {
        width: 120px;
        height: 120px;
        background: #000;
    }
    &__simon .panel:hover {
        outline: 2px solid black;
    }

    &__hud {
        margin-left: 50px;
    }

    &__look {
        color: rgb(133, 133, 0)
    }
    &__repeat {
        color: rgb(40, 143, 0)
    }
    &__fall {
        color: rgb(212, 85, 0)
    }
}

</style>