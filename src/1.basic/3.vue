<template>
    <button @click="onClick">Click</button>
</template>

<script setup>
// 2. 并行状态机
import { createMachine, interpret } from "xstate";

const machine = createMachine({
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