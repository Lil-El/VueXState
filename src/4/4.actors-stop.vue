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
                                            let count = 1;
                                            or(e => {
                                                count++;
                                                if(count == 3) {
                                                    cb("LOG")
                                                } else if(count == 4) {
                                                    cb("OVER")
                                                }
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
                    },
                    OVER: {
                        target: "over"
                    }
                },
            },
            over: {
                type: "final"
            }
        }
    }
);
const service = interpret(machine).start();

const state = service.send({ type: "FETCH" });
service.send({ type: "END", name: "MINO" });
// 发送3次“REF_PONG”，第三次触发OVER；
service.send({ type: "REF_PONG" }); // 2
service.send({ type: "REF_PONG" }); // 3
console.log(service.state.value);
{
    service.state.context.B.ref.stop(); // 停止actor；最终仍然为loading；不停止，最终为over
}
service.send({ type: "REF_PONG" }); // 4 -> over
console.log(service.state.value);

const toggle = e => {};
</script>
