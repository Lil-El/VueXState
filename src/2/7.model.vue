<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret, assign } from "xstate";
import { createModel } from "xstate/lib/model";

const userModel = createModel(
    // 初始context
    {
        name: "Mino",
        age: 25
    },
    // 初始事件
    {
        events: {
            updateName: value => ({value})
        }
    }
)

const machine = userModel.createMachine({
    context: userModel.initialContext,
    entry: userModel.assign({name: "Mini"}),
    initial: "active",
    states: {
        active: {
            on: {
                updateName: {
                    actions: userModel.assign({
                        name: (_, event) => event.value
                    })
                }
            }
        },
    }
})

const service = interpret(machine).start();
console.log(service.state.context.name);
const state = service.send({type: "updateName", value: "YXD"})
console.log(state.context.name);

const toggle = e => {};
</script>