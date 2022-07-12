<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, spawn , send, sendParent, assign } from "xstate";

const newMachine = createMachine({
    initial: "inactive",
    states: {
        inactive: {
            on: {
                "ACTIVE": {target: "active"}
            }
        },
        active: {
            after: {
                1000: {
                    actions: sendParent("LOG")
                }
            }
        }
    }
})

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
                                    // spawn接收四种entity，类型和invoke一样：callback、promise、Observable、machine
                                    // 最推荐的是使用machine创建actor
                                    ref: spawn(newMachine, "new-machine") // 生成actor
                                }
                            }
                        })
                    },
                    REF_PONG: {
                        // to：类似于invoke服务
                        actions: send("ACTIVE", {to: (c) => c.B.ref}) // 向actor发送事件
                        // actions: send("PONG", {to: "new-machine") // 或者通过name向actor发送事件
                    },
                    LOG: {
                        actions: (c, e) => {
                            console.log(c, e);
                        }
                    }
                },
            },
        },
    }
);
const service = interpret(machine).start();

const state = service.send({ type: "FETCH" });
service.send({ type: "END", name: "MINO" });
const newService = service.state.context.B.ref.start();
console.log(service, newService); // 两个actor

service.send({ type: "REF_PONG" });

const toggle = e => {};
</script>
