<template>
    <div class="screen">
        <div class="screen__inner"
        :style="{
                width: `${((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 + 16) * Math.sqrt(cardsContext.length)}px`
            }"
        >
            <card-flip 
                v-for="(card, index) in cardsContext" 
                :key="index" 
                :imgBackFaceUrl="`images/${card}.png`" 
                :card="{ index, value: card }" 
                @onFlip="checkRule($event)"
                :ref="`card-${index}`"
                :cardsContext="cardsContext"
            />
        </div>
    </div>
</template>

<script>
import CardFlip from './Card.vue';

export default {
    props: {
        cardsContext: {
            type: Array,
            default: function () {
                return [];
            }
        }
    },
    name: "InteractScreen",
    components: { CardFlip },
    data() {
        return {
            rules: []
        }
    },
    methods: {
        checkRule(card) {
            if (this.rules.length === 2) return false;

            this.rules.push(card)

            if (this.rules.length === 2 && this.rules[0].value === this.rules[1].value) {
                console.log("Right....");
                // add class 'disabled' to component card
                this.$refs[`card-${this.rules[0].index}`][0].onEnabledDisableMode()
                this.$refs[`card-${this.rules[1].index}`][0].onEnabledDisableMode()
                
                // reset rules to []
                this.rules = []

                const disabledElements = document.querySelectorAll(".screen .card.disable");
                
                if(disabledElements && disabledElements.length === this.cardsContext.length - 2) {
                    setTimeout(() => {
                        this.$emit("onFinish")
                    }, 920)
                }
            } else if (this.rules.length === 2 && this.rules[0].value !== this.rules[1].value) {
                console.log("Wrong...", this.$refs[`card-${this.rules[0].index}`]);
                setTimeout(() => {
                    // close two cards
                    this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard()
                    this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard()
    
                    // reset rules to []
                    this.rules = []
                }, 800)
            } else return false
        }
    }
}
</script>

<style lang="css" scoped>
.screen {
    width: 100%;
    height: 100vh;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 2;
    background-color: var(--dark);
    color: var(--light);
}

.screen__inner {
    display: flex;
    flex-wrap: wrap;
    margin: 2rem auto;
}
</style>