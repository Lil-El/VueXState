<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, assign, send, Machine } from "xstate";
// actions: 在一个状态进入、退出时执行动作actions
// - 1. entry
// - 2. exit
// - 3. 转换action

const lazyStubbornMachine = createMachine({
    id: "stubborn",
    initial: "inactive",
    states: {
        inactive: {
            entry() {
                console.log("entry1");
            },
            exit() {
                console.log("exit1");
            },
            on: {
                TOGGLE: {
                    target: "active",
                    // 再次向服务发送 TOGGLE 事件
                    actions: [
                        () => {
                            console.log("action");
                        },
                        // 将一个send()事件排入正在运行的服务中
                        // 这意味着该事件将在 解释（interpret） 的下一步“步骤”上发送。
                        // 即在转换之后的active上发送事件
                        // 所以最终仍然会转换为inactive
                        send("TOGGLE1") // ()=>send(...) 这么写是错误的；send()返回的是对象，不是命令式函数
                    ],
                },
            },
        },
        active: {
            entry: () => {
                console.log("entry2");
            },
            exit() {
                console.log("exit2");
            },
            on: {
                TOGGLE1: { target: "inactive" },
            },
        },
    },
});

const nextState = lazyStubbornMachine.transition("inactive", {
    type: "TOGGLE",
});
console.log(nextState.value); // active
// State 实例有一个 .actions 属性
// 它是一个供 解释（interpret） 执行的 动作 对象数组：
console.log(nextState.actions); // 从inactive转向active所要派发的action

const service = interpret(lazyStubbornMachine).start(); // 初始inactive
const finalState = service.send("TOGGLE"); // 执行toggle事件，遇到send，转换为inactive
console.log(finalState.value); // inactive

const toggle = (e) => {};
</script>