<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, assign } from "xstate";
import { createModel } from "xstate/lib/model";

const machine = createMachine(
    {
        id: "toggle",
        initial: "inactive",
        states: {
            inactive: {
                on: {
                    TOGGLE: { target: "active" },
                },
            },
            active: {
                // 只要状态机处于 'active' 状态， 'beeping' 活动就会发生
                activities: ["beeping"],
                on: {
                    TOGGLE: { target: "inactive" },
                },
            },
        },
    },
    {
        activities: {
            beeping: () => {
                const interval = setInterval(() => console.log("BEEP!"), 1000);

                return () => clearInterval(interval);
            },
        },
    }
);

const service = interpret(machine).start();

service.send({ type: "TOGGLE" });

setTimeout(() => {
    service.send({ type: "TOGGLE" });
}, 10000);

const toggle = e => {};
</script>
