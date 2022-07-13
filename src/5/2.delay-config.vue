<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, spawn, send, sendParent, assign } from "xstate";

const machine = createMachine(
    {
        initial: "inactive",
        states: {
            inactive: {
                on: {
                    ACTIVE: { target: "active" },
                },
            },
            active: {
                after: {
                    1000: {
                        target: "#MM1",
                    },
                },
            },
            state1: {
                id: "MM1",
                after: {
                    DELAY: {
                        target: "state2",
                    },
                },
            },
            state2: {
                after: [
                    {
                        delay: "CTX_DELAY",
                        target: "state3",
                    },
                ],
            },
            state3: {
                after: [
                    {
                        delay: (c, e) => c.d || 100,
                        target: "state4",
                    },
                ],
            },
            state4: {}
        },
    },
    {
        delays: {
            DELAY: 100,
            CTX_DELAY: (c, e) => {
                return c.delay || 0;
            },
        },
    }
);

const service = interpret(machine).start();

service.send({ type: "ACTIVE" });

setTimeout(() => {
    console.log(service.state.value);
}, 2000);

const toggle = e => {};
</script>
