<template>
    <div class="house">
        <div class="shafts">
            <ShaftItem 
                v-for="elevator in elevators"

                :key="elevator.id"
                :levels="levels"

                :goToLevel="elevator.level" 
                :isActive="elevator.isActive"
            />
        </div>
        <div :style="{gridTemplateRows: `repeat(${levels.length}, auto)`}" class="levels">
            <div :key="level" v-for="level in levels" class="level">
                <div class="level-info">
                    <div class="level-number"><span>{{ level }}</span> floor</div>
                    <button :style="clickedButtons.includes(level) ? {backgroundColor: '#df9000'} : null" @click="addLevelInQueue(level)" class="level-btn"></button>
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

function getElevatorsArray(count) {
    let arr = [];
    for (let i = 0; i < count; i++) {
        arr.push({id: i+1, level: 1, isActive: true});
    }
    return arr;
}

export default {
    data() {
        return {
            levels: getLevelsArray(this.levelsCount),
            elevators: getElevatorsArray(this.elevatorsCount),
            levelsQueue: [],
        }
    },
    props: [ 'levelsCount', 'elevatorsCount' ],
    components: { ShaftItem },
    methods: {
        addLevelInQueue(level) {
            this.levelsQueue.push(level);
            if (this.levelsQueue.length === 1) this.setNextLevel(this.levelsQueue[0])
        },
        setNextLevel(level) {
            let elevatorId = this.findFreeElevator(level);
            
            if (elevatorId !== 0) {
                if (this.elevators[elevatorId-1].level !== level) {
                    let currentLevel = this.elevators[elevatorId-1].level;
                    this.elevators[elevatorId-1].level = level;
                    this.elevators[elevatorId-1].isActive = false;

                    setTimeout(() => {
                        this.elevators[elevatorId-1].isActive = true;
                        if (this.levelsQueue.length > 0) this.setNextLevel(this.levelsQueue[0])
                    }, Math.abs(currentLevel - level) * 1000 + 3000)
                }
                this.levelsQueue.shift();
            }
        },
        findFreeElevator(level) {
            let tempElevators = this.elevators.filter(elevator => elevator.isActive);
            let minDifference = this.levels.length;
            let elevatorId = 0;
            for (let elevator of tempElevators) {
                if (Math.abs(level - elevator.level) < minDifference) {
                    minDifference = Math.abs(level - elevator.level);
                    elevatorId = elevator.id;
                } 
            }
            return elevatorId;
        }
    },
    // watch: {
        // 'levelsQueue': {
        //     handler() {
        //         this.setNextLevel(this.levelsQueue[0])
        //     },
        //     deep: true
        // },
        // 'elevators': {
        //     handler() {
        //         if (this.levelsQueue.length !== 0) {
        //             this.setNextLevel(this.levelsQueue[0]);
        //         }
        //     },
        //     deep: true
        // }
    // }
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
        padding: 10px 30px;
    }
    .level-info {
        display: flex;
        flex-direction: column;
        width: max-content;
        gap: 10px;
    }
    .level-number {
        font-size: 24px;
        color: #84919d;
    }
    .level-number > span {
        font-size: 44px;
        color: #84919d;
    }
    .level-btn {
        width: 25px;
        height: 25px;
        border-radius: 10px;
        border: 2px solid transparent;
        background-color: #0097a7;
        cursor: pointer;
    }
    .level-btn:hover {
        border-color: #2c3e50;
    }
</style>