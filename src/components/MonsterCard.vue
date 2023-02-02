<script>
export default {
    props: {
        monster: {
            type: Object,
            required: true
        }
    },

    computed: {
        cardColor () {
            // 0.8 * 100 = exp_80
            return `exp_${this.monster.expReduction * 100}`;
        },

        percentage() {
            return (this.monster.expReduction * 100).toFixed(0) + '%';
        },

        percentageOfLevel() {
            return (this.monster.percentageOfLevel).toPrecision(2) + '%';
        },

        formatedHP() {
            return this.monster.hp.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
        },

        countMonsters() {
            let count = 100 / this.monster.percentageOfLevel;
            return count.toFixed(0).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
        }
    },
}
</script>

<template>
    <div class="card mb-3 mx-auto" :class="cardColor" style="width: 18rem;">
        <div class="card-header d-flex justify-content-between">
            <h5 class="card-title">{{ monster.name }}</h5>
            <h5 class="card-title">{{ percentage }}</h5>
        </div>
        <div class="card-body d-flex">
            <div class="me-3" style="width: 64px;">
                <img :src="monster.img" class="img-fluid rounded-start" width="64" height="64" 
                 :alt="monster.name"/>
            </div>
            <div>
                <div>Level: {{ monster.level }}</div>
                <div>Base Exp: {{ monster.baseExp }}</div>
                <div>Real Exp: {{ monster.realExp }}</div>
                <div :title="countMonsters">Level: {{ percentageOfLevel }}</div>
                <div>HP: {{ formatedHP }}</div>
            </div>
        </div>
    </div>
</template>
