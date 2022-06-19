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
history - 上一个 State 实例
meta - 在 状态节点 的元属性上定义的任何静态元数据
done - 状态是否表示最终状态

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
                tags: "go",
                on: {
                    TIMER: "yellow",
                },
                entry: "alertGreen", // 初始为green时，调用action
                meta: {
                    message: "可以走！"
                }
            },
            yellow: {
                tags: ["go", "wait"],
                on: {
                    TIMER: {target: "red"},
                }
            },
            red: {
                type: 'compound', // type:只有final parallel hist有必写要
                initial: "walk",
                states: {
                    walk: {
                        on: {
                            STOP: {target: "stop"},
                            GOING: {target: "dead"}
                        }
                    },
                    stop: {
                        type: "atomic"
                    },
                    dead: {
                        type: "final"
                    }
                }
            }
        },
    },
    {
        actions: {
            alertGreen: (context, event) => {
                console.log(context, event);
                alert("Green!");
            },
        },
    }
);

// 扩展状态机
const sexMachine = machine.withContext({...machine.context, sex: "male"})

// 扩展状态机
const newMachine = sexMachine.withConfig({
    actions: {
        alertGreen() {
            // alert("123123")
        }
    }
})

const service = interpret(newMachine).start();

console.log(newMachine.initialState.value);
console.log(newMachine.initialState.meta);
console.log(newMachine.initialState.matches("green"))
console.log(newMachine.initialState.matches("red"))
console.log(newMachine.initialState.nextEvents)
console.log(newMachine.initialState.can("TIMER"))
console.log(newMachine.initialState.hasTag("go"))
console.log(newMachine.initialState.done)

const toggle = (e) => {
}

</script>