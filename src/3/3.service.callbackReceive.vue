<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, send } from "xstate";

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
        onReceive(e => {
            // 监听事件
            console.log("event: ", e);
            if (e.type === "PING") {
                callback("PONG");
            }
        });
    };
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
                id: "ponger",
                src: (context, event) => fetchUser(),
            },
            // 其他写法：4.actions-forward
            entry: send({ type: "PING" }, { to: "ponger" }),
            on: {
                PONG: { target: "done" },
            },
        },
        done: {
            type: "final",
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
