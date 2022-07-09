<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, assign } from "xstate";

const searchValid = (context, event, meta) => {
    console.log(context, event, meta);
    return context.canSearch && event.query && event.query.length > 0;
};

// assign 分配动作

const searchMachine = createMachine(
    {
        id: "search",
        initial: "idle",
        context: {
            canSearch: true,

            count: 0,
            message: "undefined",
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
                        actions: [
                            assign((context, event) => {
                                return {
                                    count: context.count + event.value, // 100
                                    message: "ss",
                                };
                            }),
                            context => console.log(context.count), // 101
                        ],
                        // assign是微任务优先执行；所以和预期的结果不同
                        // assign可以堆叠，顺序执行
                    },
                },
                initial: "normal",
                states: {
                    normal: {},
                    invalid: {},
                },
            },
            searching: {
                // actions
                entry: assign({
                    message: "searching",
                    count: (context, event) => context.count + 1,
                }),
            },
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
const state = service.send("SEARCH", { query: "a", value: 100 });
console.log(state.value); // true: searching   false: invalid
console.log(state.context);

const toggle = e => {};
</script>
