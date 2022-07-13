<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, spawn, send, sendParent, assign } from "xstate";

const newMachine = createMachine({
    initial: "inactive",
    states: {
        inactive: {
            on: {
                ACTIVE: { target: "active" },
            },
        },
        active: {
            after: {
                1000: [
                    {
                        target: "inactive",
                    },
                    {
                        actions: sendParent({ type: "LOG", name: "sddd" }),
                    },
                ],
                2000: {
                    actions: sendParent("LOG"),
                },
            },
            /**
             * after: [
             *      {delay: 1000, target: "", cond: xxx},
             *      {delay: 2000, target: ""}
             * ]
             */
        },
    },
});

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
            on: {
                CREATE: {
                    actions: assign({
                        ref: () =>
                            spawn(newMachine, {
                                sync: true, // 每当其状态发生变化时，Actor 都会向父级发送更新事件
                                name: "new-machine",
                            }),
                    }),
                },
                TOGGLE: {
                    actions: send("ACTIVE", { to: c => c.ref }), // 向actor发送事件
                },
                LOG: {
                    actions: (c, e) => {
                        console.log(c, e);
                    },
                },
            },
        },
    },
});
const service = interpret(machine).start();

service.send({ type: "FETCH" });
service.send({ type: "CREATE", name: "MINO" });
service.send({ type: "TOGGLE" });
const toggle = e => {};
</script>
