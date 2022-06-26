<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, actions } from "xstate";
const { forwardTo } = actions;

const lazyStubbornMachine = createMachine({
    id: "stubborn",
    initial: "inactive",
    invoke: {
        id: "sonService",
        src: createMachine({
            initial: "a",
            on: {
                TOGGLE: {
                    actions: [
                        (_, event) => {
                            if (event.type === "TOGGLE") {
                                console.log(event.message);
                            }
                        },
                    ],
                },
            },
            states: {
                a: {},
            },
        }),
        // src: () => (_, receive) => {
        //     receive((event) => {
        //         if (event.type === "TOGGLE") {
        //             console.log(event.message);
        //         }
        //     });
        // },
    },
    states: {
        inactive: {
            on: {
                TOGGLE: {
                    target: "active",
                    // 再次向服务发送 TOGGLE 事件
                    actions: [
                        () => {
                            console.log("action");
                        },
                        forwardTo("sonService"),
                    ],
                },
            },
        },
        active: {
            on: {
                TOGGLE: { actions: [() => console.log("toggle")] },
            },
        },
    },
});

const service = interpret(lazyStubbornMachine).start(); // 初始inactive
const finalState = service.send({ type: "TOGGLE", message: "hello" }); // 执行toggle事件，遇到send，转换为inactive
console.log(finalState.value);

const toggle = (e) => {};
</script>