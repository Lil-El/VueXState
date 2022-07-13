<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, actions } from "xstate";
const { cancel, send } = actions;

const machine = createMachine({
    initial: "active",
    states: {
        active: {
            on: {
                ACTIVE: {
                    actions: send("LOG", { delay: 2000, id: "TIMER" }),
                },
                LOG: {
                    actions: (c, e) => {
                        console.log(c, e);
                    },
                },
                CANCEL: {
                    actions: cancel("TIMER")
                },
            },
        },
    },
});

const service = interpret(machine).start();

service.send({ type: "ACTIVE" });

setTimeout(() => {
    service.send({ type: "CANCEL" });
}, 1000);

const toggle = e => {};
</script>
