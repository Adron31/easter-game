<template>
    <div>
        <div class="board" :class="{'disabled': !data.isActive}">
            <h2>{{ data.name }}</h2>
            <ul v-if="data.questions.length" class="board__questions questions">
                <li v-for="(question, index) in data.questions" :key="'question-' + index" class="questions__item">
                    <button type="button" class="questions__btn" @click="questionClickHandler(question)">
                        <img width="48" height="72" src="@/assets/images/lock.png" :alt="index" v-if="!question.isAnswered" class="question-icon">
                        <span v-else class="questions-completed-text">{{ index + 1 }}</span>
                    </button>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
export default {
    name: 'Board',

    props: ['data'],

    data() {
        return {
        }
    },

    mounted() {

    },

    methods: {
        questionClickHandler(question) {
            this.$emitter.emit('popup-open', question);
        },
    },
};
</script>

<style scoped lang="scss">
.board {
    display: block;
    padding: 20px 40px;
    width: 460px;
    height: 323px;
    background: url('@/assets/images/board.png') center / contain no-repeat;

    &.disabled {
        filter: brightness(0.4);
        cursor: not-allowed;

        button {
            pointer-events: none;
        }
    }

    h2 {
        margin-bottom: 20px;
        text-align: center;
        font-family: "Cabin Sketch", sans-serif;
        font-weight: 700;
        font-size: 42px;
    }
}

.questions {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    grid-gap: 16px;
    list-style: none;
    padding: 0;
    margin: 0;

    &__item {
        display: flex;
        align-items: center;
        justify-content: center;
    }

    &__btn {
        background: none;
        border: none;
        cursor: pointer;
        transition: transform 0.3s ease-in-out;

        @media (hover: hover) {
            &:hover {
                transform: scale(1.15);
            }
        }
    }

    &-completed-text {
        color: #5dca1d;
        text-align: center;
        font-family: "Cabin Sketch", sans-serif;
        font-weight: 700;
        font-size: 62px;
    }
}

.question-icon {

}
</style>
