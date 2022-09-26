<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <div class="screen">
        <!-- {{`${((((920 - (16 * 4)) / Math.sqrt(cardsContext.length) -16) * 3) / 4 
                + 16) * Math.sqrt(cardsContext.length)}px`}} -->
        <div 
            class="screen__inner"
            :style="{
                width: `${((((920 - (16 * 4)) / Math.sqrt(cardsContext.length) -16) * 3) / 4 + 16)
                * Math.sqrt(cardsContext.length)}px`
            }"
        >
            <card-flip 
                v-for="(card, index) in cardsContext" 
                :key="index" 
                :ref="`card-${index}`"
                :imgFrontFaceUrl="`images/${card}.png`"
                :card="{ index: index, value: card }"
                @onFlip="checkRule($event)"
                :cardsContext="cardsContext"
            />
        </div>
    </div>
</template>
<script>
import CardFlip from './Card.vue'

export default {
    components: {
        CardFlip,
    },
    props: {
        cardsContext: {
            type: Array,
            default: function() {
                return [];
            }
        }
    },
    data() {
        return {
            rules: [],
        }
    },
    methods: {
        checkRule(card) {
            // console.log(card);
            if (this.rules.length === 2) return false;

            this.rules.push(card);
            // console.log(this.rules);
            if (this.rules.length === 2 && this.rules[0].value === this.rules[1].value) {
                console.log("ok");
                // add class 'disabled' to component card
                setTimeout(() => {
                    this.$refs[`card-${this.rules[0].index}`][0].onEnableDisabled();
                    this.$refs[`card-${this.rules[1].index}`][0].onEnableDisabled();

                    // reset rules to []
                    this.rules = [];
                }, 800)

                const disabledElements = document.querySelectorAll(".card.disabled");
                // console.log(disabledElements);

                if (disabledElements && disabledElements.length === this.cardsContext.length - 2) {
                    setTimeout(() => {
                        this.$emit("onFinish");
                    }, 1000)
                }
            } else if (this.rules.length === 2 && this.rules[0].value !== this.rules[1].value) {
                console.log("not ok");
                setTimeout(() => {
                    // close two card
                    // refs - la 1 mang
                    this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard();
                    this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard();
                    // reset rules to []
                    this.rules = [];
                }, 800)
            } else return false;
        }
    },
}
</script>
<style scoped>
.screen {
    width: 100%;
    /* height: 100vh; */
    position: absolute;
    top: 0;
    left: 0;
    background-color: var(--dark);
    color: var(--light);
}

.screen__inner {
    display: flex;
    flex-wrap: wrap;
    margin: 2rem auto;
}
</style>