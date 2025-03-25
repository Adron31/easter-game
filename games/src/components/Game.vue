<template>
    <div class="container">
        <button
            type="button"
            class="button"
            @click="resetData"
        >
            START
        </button>
        <div class="boards">
            <Board :data="data.team1" class="board-1" :key="'team-1'"/>
            <Board :data="data.team2" class="board-2" :key="'team-2'"/>
            <QuestionPopup/>
        </div>
        <div class="main-track">
            <div class="main-track__start">
                <img src="../assets/images/start.png" alt="start" width="500" height="625">
            </div>
            <div class="hero-track">
                <Hero class="hero-track__hero hero-track__hero_1"
                      :class="{'animation': animation.team1}"
                      :img-src="heroImage1"
                      :style="`left: ${10 * data.team1.currentStep}%;`"
                />
                <Hero class="hero-track__hero hero-track__hero_2"
                      :img-src="heroImage2"
                      :class="{'animation': animation.team2}"
                      :style="`left: ${10 * data.team2.currentStep}%;`"
                />
            </div>
            <div class="footprints">
                <div v-for="n in 10" :key="'footprints-' + n" class="footprints__item">
                    <Footprints
                        class="footprints__image footprints__image_1"
                        :img-src="footprints1"
                        :style="n < data.team1.currentStep ? 'opacity: 1' : ''"
                    />
                    <Footprints
                        class="footprints__image footprints__image_2"
                        :img-src="footprints2"
                        :style="n < data.team2.currentStep ? 'opacity: 1' : ''"
                    />
                </div>
            </div>
            <div class="finish">
                <transition name="present">
                    <img src="@/assets/images/present-2.png"
                         alt="box"
                         width="160" height="164"
                         v-if="isDraw"
                         class="additional-image"
                    >
                </transition>
                <transition name="present">
                    <img src="@/assets/images/present.png"
                         alt="box"
                         width="160" height="137"
                         v-if="isWin"
                         class="present-image"
                    >
                </transition>
                <transition name="box">
                    <img src="@/assets/images/box.png"
                         alt="box"
                         width="126" height="126"
                         v-if="!isWin"
                    >
                </transition>
            </div>
        </div>
    </div>
</template>

<script>
import data from './data';
import Hero from './Hero.vue';
import Footprints from './Footprints.vue';
import Board from '@/components/Board.vue';
import QuestionPopup from '@/components/QuestionPopup.vue';
import heroImage1 from '@/assets/images/hero-1.png';
import heroImage2 from '@/assets/images/hero-2.png';
import footprints1 from '@/assets/images/footprints-1.png';
import footprints2 from '@/assets/images/footprints-2.png';

export default {
    name: 'Game',
    components: {
        Hero,
        Footprints,
        Board,
        QuestionPopup,
    },

    data() {
        return {
            heroImage1,
            heroImage2,
            footprints1,
            footprints2,
            data,
            initData: data,
            animation: {
                team1: false,
                team2: false,
            },
            isWin: false,
            isDraw: false,
        };
    },

    mounted() {
        this.restoreData();

        this.$emitter.on('answer', (question) => {
            this.data.team1.isActive = !this.data.team1.isActive;
            this.data.team2.isActive = !this.data.team2.isActive;

            if (question.isAnswered) {
                this.data[question.team].currentStep += this.data[question.team].currentStep >= this.data[question.team].questions.length ? 0 : 1;
                this.animation[question.team] = true;

                setTimeout(() => this.animation[question.team] = false, 1000);
            }

            if (this.checkWin()) {
                setTimeout(() => this.isWin = true, 1500);
            }

            if (this.checkDraw()) {
                setTimeout(() => {
                    this.isWin = true;
                    this.isDraw = true;
                }, 1500);
            }

            this.storageData(this.data);
        });
    },

    methods: {
        getImageSrc(src) {
            return new URL(src, import.meta.url).href;
        },

        storageData(data) {
            sessionStorage.setItem('gameProgress', JSON.stringify(data));
        },

        restoreData() {
            this.data = JSON.parse(sessionStorage.getItem('gameProgress')) || this.data;
        },

        resetData() {
            if (confirm('Почати спочатку?')) {
                sessionStorage.removeItem('gameProgress');
                this.data = JSON.parse(JSON.stringify(this.initData));
                this.isDraw = false;
                this.isWin = false;
            }
        },

        checkWin() {
            const maxStep = this.data.team1.questions.length;

            return (this.data.team1.currentStep >= maxStep && this.data.team2.currentStep <= maxStep - 2)
                || (this.data.team2.currentStep >= maxStep && this.data.team1.currentStep <= maxStep - 2);
        },

        checkDraw() {
            const maxStep = this.data.team1.questions.length;

            return this.data.team1.currentStep >= maxStep && this.data.team2.currentStep >= maxStep;
        },
    },
};
</script>

<style lang="scss">
.main-track {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 5;
    height: 232px;

    &__start {
        position: absolute;
        bottom: -45px;
        left: 8%;
        height: auto;
        width: 400px;

        img {
            display: block;
            height: 100%;
            width: 100%;
            object-fit: contain;
        }
    }
}

.hero-track {
    position: absolute;
    z-index: 10;
    left: 8%;
    top: 0;
    bottom: 0;
    right: 13%;

    &__hero {
        position: absolute;
        bottom: 10%;
        left: 0;
        transition: left 1s linear;

        &_1 {
            bottom: 60%;

            &.animation {
                animation: jumpUp1 1s linear alternate;
            }
        }

        &_2 {
            transform: translateX(-60%);

            &.animation {
                animation: jumpUp2 1s cubic-bezier(0.25, 1, 0.5, 1);
            }
        }
    }
}

.footprints {
    position: absolute;
    left: 14%;
    top: 0;
    bottom: 0;
    right: 10%;
    z-index: 5;
    display: flex;
    justify-content: space-between;

    &__item {
        padding-top: 30px;
    }

    &__image {
        width: 60px;
        opacity: 0;
        transition: opacity 0.5s 0.5s ease-in;

        &_1 {
            transform: translateX(140%);
        }

        &_2 {
            width: 80px;
        }
    }
}

.boards {
    position: relative;
    z-index: 1;
    padding-left: 200px;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 50px;
}

.button {
    position: fixed;
    left: 20px;
    top: 20px;
    color: #fff;
    font-family: "Chocolate Classical Sans", sans-serif;
    border: 2px solid #fff;
    border-radius: 50px;
    padding: 2px 20px 6px 20px;
    font-weight: 700;
    font-size: 18px;
    line-height: 100%;
    min-width: 117px;
    height: 44px;
    backdrop-filter: blur(15px);
    background: rgba(0, 0, 0, 0.3);
    transition: background 0.2s ease-in-out;

    @media (hover: hover) {
        &:hover {
            background: rgba(0, 0, 0, 0.6);
        }
    }
}

.finish {
    position: absolute;
    right: 40px;
    bottom: 25%;
    z-index: 30;

    img {
        position: relative;
        z-index: 3;
        grid-area: 1/1/2/2;
        max-width: 160px;
        height: auto;
    }

    .present-image {
        position: relative;
        top: 20%;
    }

    &:has(.additional-image) .present-image {
        margin-bottom: -20px;
    }

    .additional-image {
        position: absolute;
        z-index: 2;
        top: -50%;
        left: 30%;
    }
}

.present-enter-active,
.box-enter-active {
    transition: transform 0.5s ease-in-out;
}

.box-enter-to,
.present-enter-from {
    transform: scale(0);
}

.box-enter-from,
.present-enter-to {
    transform: scale(1);
}

@keyframes jumpUp1 {
    0% {
        transform: translateY(0);
    }
    10% {
        transform: translateY(-40%);
    }
    20% {
        transform: translateY(-35%);
    }
    30% {
        transform: translateY(-45%);
    }
    40% {
        transform: translateY(-40%);
    }
    50% {
        transform: translateY(-50%);
    }
}

@keyframes jumpUp2 {
    1% {
        transform: translate(-50%, 0);
    }
    23% {
        transform: translate(-50%, -20%);
    }
    47% {
        transform: translate(-50%, 0%);
    }
    53% {
        transform: translate(-50%, 0%);
    }
    78% {
        transform: translate(-50%, -20%);
    }
    99% {
        transform: translate(-50%, 0%);
    }
}
</style>
