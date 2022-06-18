<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
// 1. 有限状态机
import { createMachine, interpret } from "xstate";

// 无状态的状态机定义
// machine.transition(...) 是解释器使用的纯函数。
const toggleMachine = createMachine({
    id: "toggle",
    initial: "inactive",
    states: {
        inactive: {
            on: {
                TOGGLE: { target: "active" },
            },
        },
        active: {
            on: {
                TOGGLE: { target: "inactive" },
            },
        },
    },
});

// 具有内部状态的状态机实例
const toggleService = interpret(toggleMachine)
    .onTransition((state) => console.log(state.value))
    .start();

const toggle = () => {
    toggleService.send({ type: "TOGGLE" }); // 修改状态机实例的状态
    const state = toggleMachine.transition("active", { type: "TOGGLE" }).value; // 获取某个状态经过type之后的值
};
</script>