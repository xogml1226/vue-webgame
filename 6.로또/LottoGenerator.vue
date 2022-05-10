<template>
    <div>
        <div>당첨 숫자</div>
        <div id="결과창">
            <lotto-ball v-for="ball in winballs" :key="ball" :number="ball"></lotto-ball>
        </div>
        <div>보너스</div>
        <lotto-ball v-if="bonus" :number="bonus"></lotto-ball>
        <button v-if="redo" @click="onClickRedo">한 번 더!</button>
    </div>
</template>

<script>
import LottoBall from './LottoBall.vue';

function getWinNumbers() {
    const candidate = Array(45).fill().map((v, i) => i + 1);
    const shuffle = [];
    while (candidate.length > 0) {
      shuffle.push(candidate.splice(Math.floor(Math.random() * candidate.length), 1)[0]);
    }
    const bonusNumber = shuffle[shuffle.length - 1];
    const winNumbers = shuffle.slice(0, 6).sort((p, c) => p - c);
    return [...winNumbers, bonusNumber];
}

let timeOuts = [];

export default {
  components: { LottoBall },
    data() {
        return {
            winNumbers: getWinNumbers(),
            winballs: [],
            bonus: null,
            redo: false
        }
    },
    computed: {
       
    },
    methods: {
        onClickRedo() {
            this.winNumbers = getWinNumbers();
            this.winballs = [];
            this.bonus = null;
            this.redo = false;
        },

        showBalls() {
            for(let i=0; i<this.winNumbers.length - 1; i++) {
                timeOuts[i] = setTimeout(() => {
                    this.winballs.push(this.winNumbers[i]);
                }, (i + 1) * 1000);
            }

            timeOuts[6] = setTimeout(() => {
                this.bonus = this.winNumbers[6];
                this.redo = true;
            }, 7000);
        }
    },
    mounted() {
        this.showBalls();
    },

    beforeDestroy() {
        timeOuts.forEach((t) => {
            clearTimeout(t);
        });
    },
    
    // 왠만하면 사용 금지
    watch: {
        redo(value, oldValue) {
            if(value === false) {
                this.showBalls();
            }
        },
    },
    


};
</script>

<style scoped>
    
</style>