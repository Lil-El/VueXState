<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, assign, actions, send, Machine } from "xstate";

const searchValid = (context, event, meta) => {
    console.log(context, event, meta);
    return context.canSearch && event.query && event.query.length > 0;
};

const searchMachine = createMachine(
    {
        id: "search",
        initial: "idle",
        context: {
            canSearch: false,
        },
        states: {
            idle: {
                on: {
                    SEARCH: {
                        target: "searching",
                        // 自定义 guard 对象
                        cond: {
                            type: "searchValid",
                            minQueryLength: 3,
                        },
                    },
                },
                initial: "normal",
                states: {
                    normal: {},
                    invalid: {},
                },
            },
            searching: {},
            searchError: {},
        },
    },
    {
        guards: {
            searchValid, // 可选，如果实现没有改变
        },
    }
);
const service = interpret(searchMachine).start();
const state = service.send("SEARCH", { query: "sss" });
console.log(state.value); // true: searching   false: invalid

const toggle = e => {};
</script>
