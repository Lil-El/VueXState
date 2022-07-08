<template>
    <button @click="toggle">Click</button>
</template>

<script setup>
import {
    createMachine,
    interpret,
    assign,
    actions,
    send,
    Machine,
} from "xstate";

const searchValid = (context, event, meta) => {
    console.log(context, event);
  return context.canSearch && event.query && event.query.length > 0;
};

const searchMachine = createMachine(
  {
    id: 'search',
    initial: 'idle',
    context: {
      canSearch: false
    },
    states: {
      idle: {
        on: {
          SEARCH: [ // 多个guard，按顺序执行，直到遇到true
            {
              target: 'searching',
              // 仅当守卫 (cond) 判断为真时才过渡到“搜索”
              cond: searchValid // 或 { type: 'searchValid' }
            },
            { target: '.invalid' }
          ]
        },
        initial: 'normal',
        states: {
          normal: {},
          invalid: {}
        }
      },
      searching: {},
      searchError: {}
    }
  },
  {
    guards: {
      searchValid // 可选，如果实现没有改变
    }
  }
);
const service = interpret(searchMachine).start()
const state = service.send("SEARCH", {query: "sss"})
console.log(state.value); // true: searching   false: invalid

const toggle = (e) => {

};
</script>