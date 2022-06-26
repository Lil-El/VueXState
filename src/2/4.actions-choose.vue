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
const { choose, log } = actions;

const lazyStubbornMachine = createMachine({
    id: "stubborn",
    initial: "inactive",
    entry: choose([
        {
            cond: "g1",
            actions: [log("1")],
        },
        {
            cond: "g2",
            actions: [log("2")],
        },
    ]),
    states: {
        inactive: {
            on: {
                TOGGLE: {
                    target: "active",
                    actions: [
                        () => {
                            console.log("action");
                        },
                    ],
                },
            },
        },
        active: {
            on: {
                TOGGLE1: { target: "inactive" },
            },
        },
    },
}, {
    guards: {
        g1(){
            return false;
        },
        g2(){
            return true;
        },
    }
});
const service = interpret(lazyStubbornMachine).start(); // 初始inactive
const finalState = service.send("TOGGLE"); // 执行toggle事件，遇到send，转换为inactive

const toggle = (e) => {};
</script>