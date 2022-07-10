<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, assign } from "xstate";
import { forwardTo } from "xstate/lib/actions";

/**
 * invoke.src: 服务来源
 *      - Promise
 *      - Callback
 *      - Observables
 *      - Machine
 *      - String
 */

const fetchUser = () => {
    return (callback, onReceive) => {
        const id = setInterval(() => callback("LOG"), 1000); // 发送事件
        // 执行清理
        return () => clearInterval(id);
    }
};

const machine = createMachine({
    id: "user",
    initial: "idle",
    context: {},
    states: {
        idle: {
            on: {
                FETCH: { target: "loading" },
            },
        },
        loading: {
            invoke: {
                id: "getUser",
                src: (context, event) => fetchUser(),
            },
            on: {
                LOG: {
                    actions: () => {
                        console.log("log");
                    },
                },
                IDLE: "idle"
            },
        },
    },
});
const service = interpret(machine).start();

const state = service.send({ type: "FETCH" });
console.log(service.state.value);
setTimeout(() => {
    service.send({ type: "IDLE" });
    console.log(service.state.value);
}, 5000);

const toggle = e => {};
</script>
