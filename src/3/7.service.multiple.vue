<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, send, actions } from "xstate";
const { respond } = actions;

const minuteMachine = createMachine({
    id: "timer",
    initial: "active",

    states: {
        active: {
            on: {
                PING: {
                    actions: respond("END", { delay: 3000 }),
                },
            },
        },
    },
});

const machine = createMachine(
    {
        id: "user",
        initial: "idle",
        states: {
            idle: {
                on: {
                    FETCH: { target: "loading" },
                },
            },
            loading: {
                entry: send("PING", { to: "ponger" }),
                invoke: [ // 多个服务会同时启动
                    {
                        id: "ponger",
                        src: minuteMachine,
                    },
                    {
                        id: "ponger2",
                        src: "getUser",
                    },
                ],
                on: {
                    END: {
                        target: "end",
                    },
                    TOKEN: {
                        actions: (c, e) => {
                            console.log(c, e);
                        }
                    }
                },
            },
            end: { type: "final" },
        },
    },
    {
        // 服务配置
        services: {
            getUser: (c, e) => {
                return (callback) => {
                    callback("TOKEN")
                }
            },
        },
    }
);
const service = interpret(machine).start();

const state = service.send({ type: "FETCH" });
console.log(service.state.value);

setTimeout(() => {
    console.log(service.state.value);
}, 5000);

const toggle = e => {};
</script>
