<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, actions } from "xstate";
const { escalate, send } = actions;

const lazyStubbornMachine = createMachine({
    id: "stubborn",
    initial: "inactive",
    invoke: {
        id: "son",
        src: createMachine({
            initial: "a",
            entry: escalate({ message: "meet error" }),
            states: {
                a: {},
            },
        }),
        onEntry: {
            actions: (context, event) => {
                console.log(event.data);
            },
        },
        onError: {
            actions: (context, event) => {
                console.log(event.data);
            },
        },
    },
    states: {
        inactive: {
            on: {
                TOGGLE: {
                    target: "active",
                    actions: [
                        () => {
                            console.log("action");
                        },
                    ],
                },
            },
        },
        active: {
            on: {
                TOGGLE1: { target: "inactive" },
            },
        },
    },
});

const service = interpret(lazyStubbornMachine).start();
const finalState = service.send("TOGGLE");
const toggle = (e) => {};
</script>