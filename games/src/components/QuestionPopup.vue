<template>
    <div class="question-popup" :class="{'hidden': !isActive}">
        <transition name="fade">
            <div v-show="isActive"
                 @click="closePopup"
                 class="question-popup__overlay"
            ></div>
        </transition>
        <transition name="open">
            <div v-show="isActive" @click.stop class="question-popup__content">
                <button type="button" class="question-popup__close" @click="closePopup">
                    <IconX/>
                </button>
                <div class="question-popup__container question">
                    <h2 class="question__title">
                        {{ data.question }}
                    </h2>
                    <img :src="data.image" :alt="data.answer" width="200" height="200" class="question__img">
                    <ul class="question__options">
                        <li v-for="option in data.options"
                            :key="option"
                            class="question__option"
                        >
                            <button
                                type="button"
                                class="question__option-btn"
                                :class="{
                                    'disabled': data.isAnswered,
                                    'is-true': userAnswer === option || data.isAnswered && option === data.answer,
                                    'is-false': userAnswer === option && option !== data.answer,
                                }"
                                @click="answerHandler(option)"
                            >
                                {{ option }}
                            </button>
                        </li>
                    </ul>
                </div>
            </div>
        </transition>
    </div>
</template>

<script type="module">
import IconX from '@/components/icons/IconX.vue';

export default {
    name: 'QuestionPopup',

    components: {
        IconX,
    },

    data() {
        return {
            isActive: false,
            userAnswer: null,
            data: {},
        };
    },

    mounted() {
        this.$emitter.on('popup-open', (data) => {
            this.data = data;
            this.data.options = this.shuffleArray(this.data.options);
            this.isActive = true;
        });
    },

    methods: {
        answerHandler(answer) {
            const delay = 700;
            this.userAnswer = answer;
            this.data.isAnswered = answer === this.data.answer;

            setTimeout(() => {
                this.closePopup();
                this.$emitter.emit('answer', this.data);
            }, delay);
        },

        closePopup() {
            this.isActive = false;
            this.userAnswer = null;
        },

        shuffleArray(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }

            return array;
        },
    },
};
</script>

<style scoped lang="scss">
.question-popup {
    position: fixed;
    inset: 0;
    z-index: 999;
    display: flex;
    align-items: safe center;
    justify-content: center;
    padding: 25px;
    font-family: "Chocolate Classical Sans", sans-serif;
    font-weight: 400;

    &.hidden {
        pointer-events: none;
    }

    &__overlay {
        position: absolute;
        z-index: 1;
        inset: 0;
        background: rgba(0, 0, 0, 0.4);
    }

    &__content {
        position: relative;
        z-index: 2;
        padding: 60px 32px;
        display: flex;
        align-items: center;
        justify-content: center;
        width: 760px;
        max-width: 100%;
        min-height: 576px;
        background: radial-gradient(46.92% 46.92% at 50% 50%, rgba(0, 0, 0, 0.3) 0%, rgba(0, 0, 0, 0.1) 70%, rgba(0, 0, 0, 0) 100%), url('@/assets/images/popup-board.png') center / contain no-repeat;
        overflow: hidden;
    }

    &__close {
        position: absolute;
        top: 100px;
        right: 50px;
        z-index: 2;
        color: #fff;
        transition: 0.2s ease-in-out;

        @media (hover: hover) {
            &:hover {
                color: #a3b923;
            }
        }
    }
}

.question {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 16px;

    &__title {
        font-size: 40px;
        text-align: center;
        line-height: 1.2;
        padding: 0 60px;
    }

    &__img {
        height: 160px;
        object-fit: contain;
    }

    &__options {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 20px;
        list-style: none;
        padding: 0;
        margin: 0;
    }

    &__option-btn {
        color: #fff;
        font-family: "Chocolate Classical Sans", sans-serif;
        border: 2px solid #fff;
        border-radius: 50px;
        padding: 0 20px 6px 20px;
        font-weight: 400;
        font-size: 32px;
        line-height: 100%;
        min-width: 167px;
        height: 54px;
        backdrop-filter: blur(15px);
        background: rgba(0, 0, 0, 0.3);
        transition: background 0.2s ease-in-out;

        @media (hover: hover) {
            &:hover {
                background: rgba(0, 0, 0, 0.6);
            }
        }

        &.is-true {
            background: #a3b923;
        }

        &.is-false {
            background: #cd3333;
        }

        &.disabled {
            pointer-events: none;
            opacity: 0.7;
        }
    }
}

.open-enter-active,
.open-leave-active,
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.5s ease-in-out, visibility 0.5s ease-in-out;
}

.open-enter-from,
.open-leave-to,
.fade-enter-from,
.fade-leave-to {
    opacity: 0;
    visibility: hidden;
}

.open-leave-from,
.open-enter-to,
.fade-leave-from,
.fade-enter-to {
    opacity: 1;
    visibility: visible;
}

.open-enter-from,
.open-leave-to {
    transform: scale(0);
}

.open-leave-from,
.open-enter-to {
    transform: scale(1);
}
</style>
