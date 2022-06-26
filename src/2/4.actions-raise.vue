<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import {
    createMachine,
    interpret,
    assign,
    actions,
    send,
    Machine,
} from "xstate";
const { raise, respond } = actions;
// actions: 在一个状态进入、退出时执行动作actions
// - 1. entry
// - 2. exit
// - 3. 转换action

const lazyStubbornMachine = createMachine({
    id: "stubborn",
    initial: "inactive",
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
                        raise("TOGGLE"),
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
const finalState = service.send("TOGGLE"); // 执行toggle事件，遇到send，转换为inactive
console.log(finalState.value); // inactive

const toggle = (e) => {};
</script>