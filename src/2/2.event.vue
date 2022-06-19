<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret } from "xstate";

const machine = createMachine(
    {
        id: "light",
        initial: "unknown",
        context: {
            name: "mino",
        },
        states: {
            // 同下，Null事件 瞬间转换, 空字符串写法将在v5废弃，使用always
            unknown: {
                on: {
                    "": [
                        { target: "black", cond: "isBad" },
                        { target: "red", cond: "isRed" },
                        { target: "green", cond: "isGreen" },
                    ],
                },
            },
            // unknown: {
            //     always: [
            //         { target: "black", cond: "isBad" },
            //         { target: "red", cond: "isRed" },
            //         { target: "green", cond: "isGreen" },
            //     ],
            // },
            green: {
                on: {
                    TIMER: { target: "yellow" },
                    bad: "black", // targe简写
                    "*": "red"
                },
                entry: [
                    () => {
                        console.log("GGG");
                    },
                    "alertGreen",
                ], // 初始为green时，调用action
            },
            black: {
                on: { "": "red" }, // 空事件，直接转换为“red”
            },
            yellow: {
                on: {
                    TIMER: [
                        { target: "red", cond: "isYellow" },
                        { target: "yellow" }
                    ],
                    click: {
                        actions: [
                            (c, e) => {
                                console.log(c, e, "Yellow");
                            },
                            "alertYellow",
                        ],
                    },
                },
            },
            red: {
                type: "final",
            },
        },
    },
    {
        actions: {
            alertGreen: (context, event) => {
                console.log(context, event);
                alert("Green!");
            },
            alertYellow: (context, event) => {
                alert("alertYellow!");
            },
        },
        guards: {
            isGreen() {
                return false;
            },
            isRed() {
                return false;
            },
            isBad() {
                return false;
            },
            isYellow() {
                return !true
            }
        },
    }
);

const service = interpret(machine).start();
console.log(machine.initialState.value);
const state = machine.transition("yellow", { type: "TIMER" }); // red.walk
const state1 = machine.transition(state, { type: "GOING" });
const yellow = service.send({ type: "TIMER" });
const state3 = machine.transition("green", "bad"); // type简写，转换为black，black由于空事件，直接转换为red.walk
const state4 = machine.transition("green", "xxx"); // 事件类型匹配不到，直接执行*的target，red

const toggle = (e) => {
    console.log(e);
    const state2 = service.send(e);
    console.log(service);
};
</script>