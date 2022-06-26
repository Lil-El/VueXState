<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, actions } from "xstate";
const { respond, send } = actions;

const authServerMachine = createMachine({
    initial: "waitingForCode",
    states: {
        waitingForCode: {
            on: {
                CODE: {
                    actions: respond({ type: "TOKEN" }, { delay: 10 }),
                },
            },
        },
    },
});

const authClientMachine = createMachine({
    initial: "idle",
    states: {
        idle: {
            on: {
                AUTH: { target: "authorizing" },
            },
        },
        authorizing: {
            invoke: {
                id: "auth-server",
                src: authServerMachine,
            },
            entry: send("CODE", { to: "auth-server" }),
            on: {
                TOKEN: {
                    target: "authorized",
                    actions: [() => console.log("object")],
                },
            },
        },
        authorized: {
            type: "final",
        },
    },
});

const service = interpret(authClientMachine).start(); // 初始inactive
service.send({ type: "AUTH" }); // 执行toggle事件，遇到send，转换为inactive
console.log(service.state.value);
setTimeout(() => {
    console.log(service.state.value);
}, 1000);

const toggle = (e) => {};
</script>