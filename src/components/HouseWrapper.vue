<template>
    <div class="house">
        <div class="shafts">
            <ShaftItem 
                v-for="elevator in elevators"

                :key="elevator.id"
                :levels="levels"

                :currentLevel="elevator.level"
                :goToLevel="elevator.goTo"
                :isActive="elevator.isActive"
                :isRelaxing="elevator.isRelaxing"
            />
        </div>
        <div :style="{gridTemplateRows: `repeat(${levels.length}, auto)`}" class="levels">
            <div :key="level" v-for="level in levels" class="level">
                <div class="level-info">
                    <div class="level-number"><span>{{ level }}</span> floor</div>
                    <button 
                        :style="clickedButtons.includes(level) ? {backgroundColor: '#df9000'} : null" 
                        @click="addLevelInQueue(level)" 
                        class="level-btn">
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import ShaftItem from './ShaftItem';
import sleep from '../utils/sleep';

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
        arr.push({id: i+1, level: 1, goTo: 1, isActive: true, isRelaxing: false});
    }
    return arr;
}

export default {
    data() {
        return {
            levels: getLevelsArray(this.levelsCount),
            elevators: [],
            levelsQueue: [],
            clickedButtons: [],
        }
    },
    props: [ 'levelsCount', 'elevatorsCount' ],
    components: { ShaftItem },
    mounted() {
        if (localStorage.getItem('elevators')) {
            try {
                this.elevators = JSON.parse(localStorage.getItem('elevators'));
                this.elevators.map(elevator => {
                    elevator.isActive = true;
                    elevator.isRelaxing = false;
                    elevator.level = elevator.goTo;
                });
                if (this.elevators.length !== this.elevatorsCount) this.elevators = getElevatorsArray(this.elevatorsCount);
            } catch(e) {
                localStorage.removeItem('elevators');
            }
        } else this.elevators = getElevatorsArray(this.elevatorsCount);
        if (localStorage.getItem('levelsQueue')) {
            try {
                this.levelsQueue = JSON.parse(localStorage.getItem('levelsQueue'));
            } catch(e) {
                localStorage.removeItem('levelsQueue');
            }
        }
        if (localStorage.getItem('clickedButtons')) {
            try {
                this.clickedButtons = JSON.parse(localStorage.getItem('clickedButtons'));
            } catch(e) {
                localStorage.removeItem('clickedButtons');
            }
        }

        this.clickedButtons = [];

        if (this.levelsQueue.length > 0) {
            this.clickedButtons = this.levelsQueue;
            for (let i = 0; i < this.levelsQueue.length; i++) {
                this.setNextLevel(this.levelsQueue[0]);
            }
        }
        
    },
    methods: {
        addLevelInQueue(level) {
            if (this.elevators.some(elevator => 
                (elevator.level === level && elevator.isActive) || elevator.goTo === level
            )) return;

            if (!this.levelsQueue.includes(level)) {
                this.levelsQueue.push(level);
                this.clickedButtons.push(level);
                if (this.levelsQueue.length === 1) this.setNextLevel(this.levelsQueue[0]);
            }
        },
        async setNextLevel(level) {
            let elevatorId = this.findFreeElevator(level);

            if (elevatorId) {
                this.levelsQueue.shift();
                let currentLevel = this.elevators[elevatorId-1].level;
                this.elevators[elevatorId-1].goTo = level;
                this.elevators[elevatorId-1].isActive = false;

                await sleep(Math.abs(currentLevel - level));

                this.clickedButtons = this.clickedButtons.filter(item => item !== level);
                this.elevators[elevatorId-1].isRelaxing = true;
                this.elevators[elevatorId-1].level = this.elevators[elevatorId-1].goTo;

                await sleep(3);

                this.elevators[elevatorId-1].isActive = true;
                this.elevators[elevatorId-1].isRelaxing = false;
                if (this.levelsQueue.length > 0) this.setNextLevel(this.levelsQueue[0]);
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
    watch: {
        'elevators': {
            handler() {
                localStorage.setItem('elevators', JSON.stringify(this.elevators));
            },
            deep: true
        },
        'levelsQueue': {
            handler() {
                localStorage.setItem('levelsQueue', JSON.stringify(this.levelsQueue));
            },
            deep: true
        },
        'clickedButtons': {
            handler() {
                localStorage.setItem('clickedButtons', JSON.stringify(this.clickedButtons));
            },
            deep: true
        }
    }
}
</script>

<style scoped>
    .house {
        min-height: 100vh;
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