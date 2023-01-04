<template>
    <div class="screen">
        <div class="screen__inner"
            :style="{ width: `${((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 + 16) * Math.sqrt(cardsContext.length)}px` }">
            <CardFlip v-for="(card, index) in cardsContext" :key="index" :ref="`card-${index}`"
                :imgBackFaceUrl="`../src/assets/img/${card}.png`" @onFlip="checkRules($event)"
                :card="{ index, value: card }" :cardsContext="cardsContext" />
        </div>
    </div>
</template>
<script>
import CardFlip from "./card.vue"
export default {
    props: {
        cardsContext: {
            type: Array,
            default: function () { return [] }
        }
    },
    components: {
        CardFlip
    },
    data() {
        return {
            rules: []
        }
    },
    methods: {
        checkRules(card) {

            if (this.rules.length === 2) return false

            this.rules.push(card)

            if (this.rules.length === 2 && this.rules[0].value === this.rules[1].value) {
                this.$refs[`card-${this.rules[0].index}`][0].onEnabledMode()
                this.$refs[`card-${this.rules[1].index}`][0].onEnabledMode()
                this.rules = []

                const disabledElements = document.querySelectorAll('.screen .card.disabled')
                if (disabledElements && disabledElements.length == this.cardsContext.length - 2) {
                    setTimeout(() => {
                        this.$emit('onFinish')
                    }, 1000);
                }
            } else if (this.rules.length === 2 && this.rules[0].value !== this.rules[1].value) {
                setTimeout(() => {
                    this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard()
                    this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard()
                    this.rules = []
                }, 800)
            } else return false
        }
    }
}
</script>
<style scoped lang="css">
.screen {
    width: 100%;
    height: 100vh;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 2;
    background: var(--dark);
    color: var(--ligth);
}

.screen__inner {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
    margin: 1rem auto;
}
</style>