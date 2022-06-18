<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import { createMachine, interpret } from "xstate";

/**
 * 

actions - action 名称到它们的执行的映射
delays - delays 名称与其执行的映射
guards - 转换守卫 (cond) ，名称与其执行的映射
services - 调用的服务 (src) ，名称与其执行的映射
activities (deprecated) - activities 名称与其执行的映射

 */
const machine = createMachine(
    {
        id: "light",
        initial: "green",
        context: {
            name: "mino",
        },
        states: {
            green: {
                entry: "alertGreen", // 初始为green时，调用action
                off: "alertGreen",
            },
        },
    },
    {
        actions: {
            alertGreen: (context, event) => {
                console.log(context, event);
                alert("Green!");
            },
        },
        delays: {
            /* ... */
        },
        guards: {
            /* ... */
        },
        services: {
            /* ... */
        },
    }
);

// 扩展状态机
const sexMachine = machine.withContext({...machine.context, sex: "male"})

// 扩展状态机
const newMachine = sexMachine.withConfig({
    actions: {
        alertGreen() {
            alert("123123")
        }
    }
})

const service = interpret(newMachine)
    .onTransition(() => {
        console.log("object");
    })
    .start();

const toggle = () => {
    console.log(service);
}

</script>