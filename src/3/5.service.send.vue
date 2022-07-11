<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, send, sendParent } from "xstate";

const minuteMachine = createMachine({
    id: "timer",
    initial: "active",

    states: {
        active: {
            on: {
                PING: {
                    actions: [
                        sendParent("PONG", { delay: 1000 }),
                        () => {
                            console.log("PING");
                        },
                    ],
                },
            },
        },
    },
});

const machine = createMachine({
    id: "user",
    initial: "idle",
    states: {
        idle: {
            on: {
                FETCH: { target: "loading" },
            },
        },
        loading: {
            entry: send({ type: "PING", name: "MINO" }, { to: "ponger" }),
            invoke: {
                id: "ponger",
                src: minuteMachine,
            },
            on: {
                PONG: {
                    actions: [
                        send("PING", { delay: 1000, to: "ponger" }),
                        () => {
                            console.log("PONG");
                        },
                    ],
                },
            },
        },
    },
});
const service = interpret(machine).start();

const state = service.send({ type: "FETCH" });
console.log(service.state.value);

const toggle = e => {};
</script>
