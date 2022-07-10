<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, assign } from "xstate";

// 一台状态机表达整个应用程序的行为会变得复杂和笨拙
// 使用多个相互通信的状态机来表达复杂的逻辑是很自然的 invoke实现状态机之间的相互通信
/**
 * invoke.src: 服务来源
 *      - Promise
 *      - Callback
 *      - Observables
 *      - Machine
 *      - String
 * invoke.onDone 表示正在完成的调用；
 * machine.onDone 表示最终状态；两者表达意思不同
 */

const fetchUser = () => {
    return new Promise((r, j) => {
        setTimeout(() => {
            // r({ name: "Mino" });
            j(500);
        }, 1000);
    });
};

const machine = createMachine({
    id: "user",
    initial: "idle",
    context: {
        userId: 42,
        user: undefined,
        error: undefined,
    },
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
                onDone: {
                    // 正在完成的调用
                    target: "success",
                    actions: assign({
                        user: (context, event) => {
                            console.log("ss:", event.data);
                            return event.data;
                        },
                    }),
                },
                onError: {
                    target: "failure.500",
                    actions: assign({
                        error: (context, event) => {
                            console.log("ff:", event.data);
                            return event.data;
                        },
                    }),
                },
            },
        },
        success: {},
        failure: {
            initial: "401",
            entry: (c, e) => {
                console.log(c, e);
            },
            states: {
                401: {},
                404: {},
                500: { type: "final" },
            },
            onDone: "over", // 最终状态 转换到over
        },
        over: {},
    },
});
const service = interpret(machine).start();

const state = service.send({ type: "FETCH" });
console.log(service.state.value);
setTimeout(() => {
    console.log(service.state.value);
    // console.log(service.state.context);
    // console.log(service);
}, 2000);

const toggle = e => {};
</script>
