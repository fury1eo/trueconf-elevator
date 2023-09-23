<template>
    <div class="shaft">
        <div 
            class="elevator" 
            :style="!elevatorRelaxing
                ? {transition: `${levelDifference}s all ease`, bottom: `${height}vh`, height: `calc(100vh / ${levels.length})`}
                : {animation: 'flash 3s', bottom: `${height}vh`, transition: `${levelDifference}s all ease`, height: `calc(100vh / ${levels.length})`}"
        >
            <div v-if="!isActive" class="elevator_info">
                <div class="level_number">{{ level }}</div>
                <span class="material-symbols-outlined">
                    {{direction === 'up' ? 'arrow_upward' : 'arrow_downward'}}
                </span>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                level: 1,
                levelDifference: 0,
                elevatorRelaxing: false,
                height: 100 / this.levels.length * (this.goToLevel-1),
                direction: 'up'
            }
        },
        props: [ 'levels', 'goToLevel', 'isActive'],
        watch: {
            'goToLevel': {
                handler() {
                    this.levelDifference = Math.abs(this.goToLevel - this.level);

                    this.direction = this.goToLevel > this.level ? 'up' : 'down';

                    this.height = 100 / this.levels.length * (this.goToLevel-1);
                    this.level = this.goToLevel;

                    setTimeout(() => {
                        this.elevatorRelaxing = true;
                        setTimeout(() => {
                            this.elevatorRelaxing = false;
                        }, 3000)
                    }, this.levelDifference * 1000)
                }
            }
        }
    }
</script>

<style>
    .shaft {
        position: relative;
        width: 180px;
        height: 100vh;
        border-left: 2px solid #2c3e50;
        border-right: 2px solid #2c3e50;
    }
    .elevator {
        padding: 10px;
        left: 50%;
        /* bottom: 0vh; */
        transform: translateX(-50%);
        width: 80%;
        background-color: #0097a7;
        border-radius: 10px;
        position: absolute;
        color: #fff;
    }
    .elevator_info {
        display: flex;
        align-items: center;
        gap: 2px;
    }
    .level_number {
        color: #fff;
    }
    .material-symbols-outlined {
        color: #fff;
    }
    @keyframes flash {
        0% {
            opacity: 1;
        }
        25% {
            opacity: .5;
        }
        50% {
            opacity: 1;
        }
        75% {
            opacity: .5;
        }
        100% {
            opacity: 1;
        }
    }
</style>