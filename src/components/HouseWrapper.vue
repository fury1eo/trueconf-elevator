<template>

    <div class="house">
        <div class="shafts">
            <ShaftItem 
                v-for="lift in lifts"

                :key="lift.id"
                :levels="levels"

                :goToLevel="lift.level" 
                :isActive="lift.isActive"
            />
        </div>
        <div :style="{gridTemplateRows: `repeat(${levels.length}, auto)`}" class="levels">
            <div :key="level" v-for="level in levels" class="level">
                <div class="level-info">
                    <div class="level-number">{{ level }}</div>
                    <button @click="setNextLevel(level)" class="level-btn"></button>
                </div>
            </div>
        </div>
    </div>

</template>

<script>

import ShaftItem from './ShaftItem';

function getLevelsArray(count) {
    let arr = [];
    for (let i = count; i > 0; i--) {
        arr.push(i);
    }
    return arr;
}

function getLiftsArray(count) {
    let arr = [];
    for (let i = 0; i < count; i++) {
        arr.push({id: i, level: 1, isActive: true});
    }
    return arr;
}

export default {

    data() {
        return {
            levels: getLevelsArray(this.levelsCount),
            lifts: getLiftsArray(this.liftsCount)
        }
    },

    props: [ 'levelsCount', 'liftsCount' ],
    components: { ShaftItem },
    methods: {
        setNextLevel(level) {
            let liftId = this.findFreeLift(level);
            if (this.lifts[liftId].isActive && this.lifts[liftId].level !== level) {
                let currentLevel = this.lifts[0].level;
                this.lifts[liftId].level = level;
                this.lifts[liftId].isActive = false;

                setTimeout(() => {
                    this.lifts[liftId].isActive = true;
                }, Math.abs(currentLevel - level) * 1000 + 3000)
            }
        },
        findFreeLift(level) {
            let tempLifts = this.lifts.filter(lift => lift.isActive);
            let minDifference = this.levels.length;
            let liftId = 0;
            for (let lift of tempLifts) {
                if (Math.abs(level - lift.level) < minDifference) {
                    minDifference = Math.abs(level - lift.level);
                    liftId = lift.id;
                } 
            }
            return liftId;
        }
    },

}

</script>

<style scoped>
    .house {
        height: 100vh;
        display: flex;
    }
    .shafts {
        display: flex;
        gap: 10px;
    }
    .levels {
        display: grid;
        width: 100%;
    }
    .level {
        border-bottom: 1px solid #2c3e50;
        display: grid;
        padding: 10px;
    }
    .level-info {
        display: flex;
        flex-direction: column;
        gap: 10px;
    }
    .level-btn {
        width: 20px;
        height: 20px;
        border-radius: 50%;
        border: 2px solid transparent;
        background-color: #0097a7;
        cursor: pointer;
    }
    .level-btn:hover {
        border: 2px solid #2c3e50;
    }
</style>