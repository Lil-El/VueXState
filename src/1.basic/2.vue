<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
// 2. 分层(嵌套)状态机
import { createMachine, interpret } from "xstate";

const actionState = {
    initial: "walk",
    states: {
        walk: {
            on: {
                CHANGE: {target: "run"}
            },
            initial: "see",
            states: {
                see: {
                    on: {
                        PHONE: {target: "notSee"}
                    }
                },
                notSee: {
                    on: {
                        PHONE: {target: "see"}
                    }
                }
            }
        },
        run: {
            on: {
                CHANGE: {target: "walk"}
            }
        }
    }
}

const machine = createMachine({
    initial: "green",
    states: {
        green: {
            on: {
                TIMEOUT: { target: "yellow" },
            },
        },
        yellow: {
            on: {
                TIMEOUT: { target: "red" },
            },
        },
        red: {
            on: {
                TIMEOUT: { target: "green" },
            },
            ...actionState
        },
    },
});

const service = interpret(machine)
    .onTransition((state) => console.log(state.value))
    .start();
    
const toggle = () => {
    // service.send({ type: "TIMEOUT" }); // 修改状态机实例的状态
    const state = machine.transition('yellow', { type: 'TIMEOUT' }).value; // red : walk : see
    const state2 = machine.transition('red.walk', { type: 'CHANGE' }).value; // red : run
    const state3 = machine.transition('red.walk.see', { type: 'PHONE' }).value; // red : walk : notSee
    const state4 = machine.transition('red.walk.see', { type: 'CHANGE' }).value; // red : run
    const state5 = machine.transition({red: {walk: "see"}}, { type: 'CHANGE' }).value; // red : run
};
</script>