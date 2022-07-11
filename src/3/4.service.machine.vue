<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, send, assign } from "xstate";

/**
 * send / sendParent: 父传子 、 子传父
 * 子状态机可以使用从父状态机的 context 和 data 属性派生的 context invoke调用
 */

const minuteMachine = createMachine({
    id: "timer",
    initial: "active",
    context: {
        duration: 2000,
    },
    states: {
        active: {
            after: {
                2000: { target: "finished" },
            },
        },
        finished: {
            type: "final",
            data: {
                you: (context, event) => {
                    // 子状态机可以使用从父状态机的 context 和 data 属性派生的 context 调用
                    console.log(context, event);
                    return 250;
                },
            },
        },
    },
});

const machine = createMachine({
    id: "user",
    initial: "idle",
    context: {
        customDuration: 3000,
    },
    states: {
        idle: {
            on: {
                FETCH: { target: "loading" },
            },
        },
        loading: {
            invoke: {
                id: "ponger",
                src: minuteMachine,
                data: (context, event) => {
                    // data替代context，返回新的数据，而不会合并
                    console.log(context);
                    return { name: "MM" };
                },
                /**
                 * data: {
                 *      foo: "foo",
                 *      bar: event.value,
                 *      dur: () => ();
                 * }
                 */
                onDone: {
                    target: "end",
                    actions: assign({
                        revealedSecret: (context, event) => {
                            console.log(event);
                            // event is:
                            // { type: 'done.invoke.secret', data: { secret: '42' } }
                            return event.data.secret;
                        },
                    }),
                },
            },
        },
        end: {},
    },
});
const service = interpret(machine).start();

const state = service.send({ type: "FETCH" });
console.log(service.state.value);
setTimeout(() => {
    console.log(service.state.value);
}, 2000);
setTimeout(() => {
    console.log(service.state.value);
}, 4000);

const toggle = e => {};
</script>
