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
                                    ref: spawn((cb, or) => {
                                            or(e => {
                                                console.log(e)
                                                cb("LOG")
                                            })
                                        }) // 生成actor
                                    // ref: spawn(newMachine, "new-machine") // 生成actor
                                    // ref: spawn(newMachine, "new-machine") // 生成actor
                                }
                            }
                        })
                    },
                    REF_PONG: {
                        // to：类似于invoke服务
                        actions: send("PONG", {to: (c) => c.B.ref}) // 向actor发送事件
                        // actions: send("PONG", {to: "new-machine") // 或者通过name向actor发送事件
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
