<template>

    <div class="shaft">
        <div 
            class="lift" 
            :style="!liftRelaxing
                ? {transition: `${levelDifference}s all ease`, bottom: `${height}vh`, height: `calc(100vh / ${levels.length})`}
                : {animation: 'flash 3s', bottom: `${height}vh`, height: `calc(100vh / ${levels.length})`}"
        >
            <div v-if="!isActive" class="lift_info">
                <div class="level_number">{{ goToLevel }}</div>
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
                liftRelaxing: false,
                height: 100 / this.levels.length * (this.goToLevel-1)
            }
        },

        props: [ 'levels', 'goToLevel', 'isActive'],
        watch: {
            'goToLevel': {
                handler() {
                    this.levelDifference = Math.abs(this.goToLevel - this.level);
                    this.height = 100 / this.levels.length * (this.goToLevel-1);
                    this.level = this.goToLevel;

                    setTimeout(() => {
                        this.liftRelaxing = true;
                        setTimeout(() => {
                            this.liftRelaxing = false;
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
    .lift {
        padding: 10px;
        left: 50%;
        bottom: 0vh;
        transform: translateX(-50%);
        width: 80%;
        background-color: #0097a7;
        border-radius: 10px;
        position: absolute;
        color: #fff;
    }
    .level_number {
        color: #fff;
    }
    @keyframes flash {
        0% {
            background-color: rgba(0, 151, 167, 1);
        }
        25% {
            background-color: rgba(0, 151, 167, .5);
        }
        50% {
            background-color: rgba(0, 151, 167, 1);
        }
        75% {
            background-color: rgba(0, 151, 167, .5);
        }
        100% {
            background-color: rgba(0, 151, 167, 1);
        }
    }
</style>