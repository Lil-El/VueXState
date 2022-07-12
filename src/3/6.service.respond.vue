<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, send, actions } from "xstate";
const { respond } = actions;
// respond(...) 会将一个事件发送回接收到的事件的来源，它可能不一定是父状态机

const minuteMachine = createMachine({
    id: "timer",
    initial: "active",

    states: {
        active: {
            on: {
                PING: {
                    actions: respond("END", {delay: 3000}),
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
            /**
             * 多服务
             * invoke: [
             *      {id: "", src: xxx},
             *      {id: "", src: yyy},
             * ]
             */
            on: {
                END: {
                    target: "end"
                },
            },
        },
        end: {type: "final"}
    },
});
const service = interpret(machine).start();

const state = service.send({ type: "FETCH" });
console.log(service.state.value);

setTimeout(() => {
    console.log(service.state.value);
} , 5000)

const toggle = e => {};
</script>
