<script>
import { mobsDB, expDB } from '../db.js';
import MonsterCard from '../components/MonsterCard.vue'

export default {
    components: {
        MonsterCard
    },

    data() {
        return {
            player: {
                level:  65,
                frendshipBlessing: 1,
                isSpooky: true,
                isHourglass: true,
                showExpReduction_20: true,
                showExpReduction_40: true,
                showExpReduction_60: true,
                showExpReduction_80: true,
                showExpReduction_90: true,
                showExpReduction_95: true,
            },
            monsters: [],
        }
    },

    created() {
        this.monsters = mobsDB.reverse();
        this.loadPlayer();
        this.recalcMonsters();
    },
    
    methods: {
        recalcMonsters() {
            this.monsters.forEach(this.recalcMonster);
        },
        
        recalcMonster(monster) {
            monster.expReduction = this.calcExpReduction(monster);
            monster.realExp = this.calcRealExp(monster);
            monster.percentageOfLevel = this.calcPercentageOfLevel(monster);
        },

        calcExpReduction(monster) {
            let delta = this.player.level - monster.level;
            switch (true) {
                case (delta <= 10): return 1;
                case (delta <= 15): return 0.8;
                case (delta <= 20): return 0.6;
                case (delta <= 25): return 0.4;
                case (delta <= 30): return 0.2;
                case (delta <= 50): return 0.1;
                default: return 0.05;
            }
        },

        calcRealExp(monster) {
            let realExp = monster.baseExp * this.player.frendshipBlessing;
            if (this.player.isSpooky) realExp = realExp * 1.05;
            if (this.player.isHourglass) realExp = realExp * 2;
            realExp = realExp * monster.expReduction;
            return Math.ceil(realExp);
        },

        calcPercentageOfLevel(monster) {
            return monster.realExp / expDB[this.player.level] * 100;
        },

        isFilterPass(monster) {
            return (monster.expReduction === 1) 
                || (monster.expReduction === 0.8  && this.player.showExpReduction_20) 
                || (monster.expReduction === 0.6  && this.player.showExpReduction_40) 
                || (monster.expReduction === 0.4  && this.player.showExpReduction_60) 
                || (monster.expReduction === 0.2  && this.player.showExpReduction_80)
                || (monster.expReduction === 0.1  && this.player.showExpReduction_90)
                || (monster.expReduction === 0.05 && this.player.showExpReduction_95);
        },

        savePlayer() {
            localStorage.setItem('player', JSON.stringify(this.player));
        },

        loadPlayer() {
            if(localStorage.hasOwnProperty('player')) {
                this.player = JSON.parse(localStorage.getItem('player'));
            }
        }
    },

    watch:{
        player: {
            deep: true,
            handler(){
                this.savePlayer();
                this.recalcMonsters();
            }
        }
    },

    computed: {
        filtredMonsters() {
            return this.monsters.filter(this.isFilterPass);
        }
    }
}
</script>


<template>
    <div class="container border ">

        <div class="d-flex align-content-center flex-wrap mt-4">

            <!-- player.level -->
            <div class="mb-4 mx-auto" style="width: 18rem;">
                <label for="levelEdit" class="form-label">Your base level (between 1 and 99):</label>
                <input type="number" class="form-control" id="levelEdit"  min="1" max="99" v-model="player.level">
                <input type="range"  class="form-range"   id="lavelRange" min="1" max="99" v-model="player.level">
            </div>

            <!-- player.frendshipBlessing -->
            <div class="mb-4 mx-auto" style="width: 18rem;">
                <label for="frendshipBlessing" class="form-label">Your today's Frendship Blessing:</label>
                <select class="form-select" id="frendshipBlessing" v-model="player.frendshipBlessing">
                    <option value="1">No XP bonus</option>
                    <option value="1.03">Base XP +3%</option>
                    <option value="1.05">Base XP +5%</option>
                </select>
            </div>

            <!-- player.isSpooky & player.isHourglass -->
            <div class="mb-4 mx-auto" style="width: 18rem;">
                <label class="form-label">Do you have these active buffs:</label>
                <!-- Hourglass -->
                <div class="form-check">
                    <input class="form-check-input" type="checkbox" id="hourglassCheck" v-model="player.isHourglass">
                    <label class="form-check-label" for="hourglassCheck">
                        Hourglass (x2 XP)
                    </label>
                </div>
                <!-- Spooky Pass -->
                <div class="form-check">
                    <input class="form-check-input" type="checkbox" id="spookyCheck" v-model="player.isSpooky">
                    <label class="form-check-label" for="spookyCheck">
                        Spooky Pass (+5% XP)
                    </label>
                </div>
            </div>

            <!-- Filters -->
            <div class="mb-4 mx-auto" style="width: 18rem;">
                <!-- <label class="form-label">Show monsters with:</label><br> -->
                <label class="form-label">Show only monsters:</label><br>
                <!-- exp_reduction_20 -->
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="checkbox" id="exp_reduction_20" v-model="player.showExpReduction_20">
                    <label class="form-check-label" for="exp_reduction_20">80% XP</label>
                </div>
                <!-- exp_reduction_40 -->
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="checkbox" id="exp_reduction_40" v-model="player.showExpReduction_40">
                    <label class="form-check-label" for="exp_reduction_40">60% XP</label>
                </div>
                <!-- exp_reduction_60 -->
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="checkbox" id="exp_reduction_60" v-model="player.showExpReduction_60">
                    <label class="form-check-label" for="exp_reduction_60">40% XP</label>
                </div>
                <!-- exp_reduction_80 -->
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="checkbox" id="exp_reduction_80" v-model="player.showExpReduction_80">
                    <label class="form-check-label" for="exp_reduction_80">20% XP</label>
                </div>
                <!-- exp_reduction_90 -->
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="checkbox" id="exp_reduction_90" v-model="player.showExpReduction_90">
                    <label class="form-check-label" for="exp_reduction_90">10% XP</label>
                </div>
                <!-- exp_reduction_95 -->
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="checkbox" id="exp_reduction_95" v-model="player.showExpReduction_95">
                    <label class="form-check-label" for="exp_reduction_95">5% XP</label>
                </div>
            </div>            

        </div>

        <div class="d-flex align-content-center flex-wrap">
            <monster-card v-for="monster in filtredMonsters" :key=monster.name :monster="monster" />
        </div>

    </div>
</template>