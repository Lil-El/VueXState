<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret } from "xstate";
// 自转换：内部转换和外部转换
// 内部转换：不会再次执行在父状态节点上定义的 entry, exit 或者任何其他的 actions。
const machine = createMachine(
    {
        id: "light",
        initial: "green",
        states: {
            green: {
                initial: "g1",
                states: {
                    g1: {
                        entry: "alertG1",
                        exit: () => {
                            console.log("e:G1");
                        },
                    }, // 通过intercept触发
                    g2: {
                        entry: "alertG2",
                        exit: () => {
                            console.log("e:G2");
                        },
                    },
                },
                entry: "alertG",
                exit: () => {
                            console.log("e:G");
                        },
                on: {
                    // 外部转换: 将执行父状态的 exit 和 entry 操作。
                    self: ".g2",
                    wrap: "green.g1", // 外部转换
                    where: "#light.green.g2",
                    // multiple: ["green.g1", "yellow"] 多个目标，需要开启parallel
                    // wrap: {
                    //     target: ".g2", // green.g2
                    //     internal: false
                    // },
                },
            },
            yellow: {
                initial: "y1",
                states: {
                    y1: {},
                    y2: {},
                },
                on: {
                    self: {
                        target: ".y2", // 内部转换到子状态
                    },
                    // EVENT: '.foo' - 内部转换到子状态
                    // EVENT: { target: '.foo' } - 内部转换到子状态（以'.'开头）
                    // EVENT: undefined - 禁止转换
                    // EVENT: { actions: [ ... ] } - 内部自转换
                    // EVENT: { actions: [ ... ], internal: true } - 内部自转换，同上
                    // EVENT: { target: undefined, actions: [ ... ] } - 内部自转换，同上
                    TIMER: "green",
                    click: {
                        actions: [
                            (c, e) => {
                                console.log(c, e, "Yellow");
                            },
                        ],
                        target: undefined, // 自转换：不写也一样
                        internal: true, // 自转换：不写也一样
                    },
                },
            },
        },
    },
    {
        actions: {
            alertG() {
                console.log("G");
            },
            alertG1() {
                console.log("G1");
            },
            alertG2() {
                console.log("G2");
            },
        },
    }
);
const service = interpret(machine).start();
const state = service.send("self") // exit退出g1，entry进入g2
console.log("转换为", state.value);
const state2 = service.send("wrap");
console.log("转换为", state2.value);

// const state = machine.transition("green", "where") // 任意转换

const toggle = (e) => {};
</script>