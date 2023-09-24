<template>
    <div class="shaft">
        <div 
            class="elevator" 
            :style="!isRelaxing
                ? {transition: `${levelDifference}s all linear`, bottom: `${height}%`, height: `calc(100% / ${levels.length})`}
                : {animation: 'flash 3s', bottom: `${height}%`, transition: `${levelDifference}s all linear`, height: `calc(100% / ${levels.length})`}"
        >
            <div v-if="!isActive" class="elevator_info">
                <div class="level_number">{{ goToLevel }}</div>
                <span class="material-symbols-outlined">
                    {{ `arrow_${direction}ward` }}
                </span>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                levelDifference: Math.abs(this.goToLevel - this.currentLevel),
                height: 100 / this.levels.length * (this.goToLevel-1),
                direction: 'up'
            }
        },
        props: [ 'levels', 'currentLevel', 'goToLevel', 'isActive', 'isRelaxing'],
        watch: {
            'goToLevel': {
                handler() {
                    if (this.goToLevel !== this.currentLevel) {
                        this.levelDifference = Math.abs(this.goToLevel - this.currentLevel);
                        this.direction = this.goToLevel > this.currentLevel ? 'up' : 'down';
                        this.height = 100 / this.levels.length * (this.goToLevel-1);
                    }
                },
                immediate: true
            }
        }
    }
</script>

<style>
    .shaft {
        position: relative;
        width: 180px;
        height: 100%;
        border-left: 2px solid #2c3e50;
        border-right: 2px solid #2c3e50;
    }
    .elevator {
        padding: 10px;
        left: 50%;
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