<template>
    <button @click="onClick">Click</button>
</template>

<script setup>
// 2. 并行状态机
import { createMachine, interpret } from "xstate";

const machine = createMachine({
    /**
     * 状态节点类型：
     *  atomic 原子状态节点没有子状态。 （即，它是一个叶节点。）
        compound 复合状态节点包含一个或多个子 states，并有一个 initial 状态，这是这些子状态之一的 key。
        parallel 并行状态节点包含两个或多个子 states，并且没有初始状态，因为它表示同时处于其所有子状态。
        final 最终状态节点是代表抽象“终端”状态的叶节点。
        history 历史状态节点是一个抽象节点，表示解析到其父节点最近的浅或深历史状态。
     */
    type: "parallel",
    states: {
        bold: {
            initial: "off",
            states: {
                on: {
                    on: {
                        TOGGLE_BOLD: { target: "off" },
                    },
                },
                off: {
                    on: {
                        TOGGLE_BOLD: { target: "on" },
                    },
                },
            },
        },
        underline: {
            initial: "off",
            states: {
                on: {
                    on: {
                        TOGGLE_UNDERLINE: { target: "off" },
                    },
                },
                off: {
                    on: {
                        TOGGLE_UNDERLINE: { target: "on" },
                    },
                },
            },
        },
        italics: {
            initial: "off",
            states: {
                on: {
                    on: {
                        TOGGLE_ITALICS: { target: "off" },
                    },
                },
                off: {
                    on: {
                        TOGGLE_ITALICS: { target: "on" },
                    },
                },
            },
        },
        list: {
            initial: "none",
            states: {
                none: {
                    on: {
                        BULLETS: { target: "bullets" },
                        NUMBERS: { target: "numbers" },
                    },
                },
                bullets: {
                    on: {
                        NONE: { target: "none" },
                        NUMBERS: { target: "numbers" },
                    },
                },
                numbers: {
                    on: {
                        BULLETS: { target: "bullets" },
                        NONE: { target: "none" },
                    },
                },
            },
        },
    },
});

const onClick = () => {
    const state = machine.transition("bold.off", { type: "TOGGLE_BOLD" }).value;
    const state2 = machine.transition(
        {
            bold: "off",
            italics: "off",
            underline: "on",
            list: "bullets",
        },
        { type: "TOGGLE_ITALICS" }
    ).value;
};
</script>