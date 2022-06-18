<template>
    <button @click="onClick">Click</button>
</template>

<script setup>
// 2. 历史状态
import { createMachine, interpret } from "xstate";

const machine = createMachine({
    initial: "method",
    states: {
        method: {
            initial: "cash",
            states: {
                cash: {
                    on: {
                        SWITCH_CHECK: { target: "check" },
                    },
                },
                check: {
                    on: {
                        SWITCH_CASH: { target: "cash" },
                    },
                },
                hist: { type: "history" },
            },
            on: {
                NEXT: { target: "review" },
            },
        },
        review: {
            on: {
                PREVIOUS: { target: "method.hist" },
            },
        },
    },
});

const onClick = () => {
    const checkState = machine.transition("method.cash", {
        type: "SWITCH_CHECK",
    });
    const cashState = machine.transition(checkState, {
        type: "SWITCH_CASH",
    });
    console.log(cashState);
    // => State {
    //   value: { method: 'check' },
    //   history: State { ... }
    // }

    const reviewState = machine.transition(cashState, { type: "NEXT" });
    console.log(reviewState);

    // => State {
    //   value: 'review',
    //   history: State { ... }
    // }

    const previousState = machine.transition(reviewState, {
        type: "PREVIOUS",
    }).value;
    console.log(previousState);
    // => { method: 'check' }
};
</script>