<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, spawn , send, assign } from "xstate";

const machine = createMachine(
    {
        id: "user",
        initial: "idle",
        context: {},
        states: {
            idle: {
                on: {
                    FETCH: { target: "loading" },
                },
            },
            loading: {
                on: {
                    END: {
                        actions: assign({
                            A: "a",
                            B: (context, event) => {
                                return {
                                    ev: event.name,
                                    // 就像 invoking promises 一样，promise 可以作为 演员 生成。 
                                    // 发送回状态机的事件将是一个 'done.invoke.<ID>' 操作
                                    // promise 响应作为有效负载中的 data 属性：
                                    ref: spawn(new Promise((r, j) => {
                                        setTimeout(()=>{
                                            r("LOG") // TODO: 不知道这里如何操作了
                                        }, 1000)
                                    }), "xx") // 生成actor: 不建议生成promise的actor
                                }
                            }
                        }),
                    },
                    REF_PONG: {
                        actions: send("PONG", {to: (c) => c.B.ref}) // 向actor发送事件
                    },
                    LOG: {
                        actions: (c, e) => {
                            console.log(c, e);
                        }
                    }
                },
            },
        }
    }
);
const service = interpret(machine).start();

const state = service.send({ type: "FETCH" });
console.log(service.state.value);
service.send({ type: "END", name: "MINO" });

service.send({ type: "REF_PONG" });

const toggle = e => {};
</script>
