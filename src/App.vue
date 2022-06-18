<template>
    <hello-world name="Mino" />
</template>

<script>
import { createMachine, interpret } from "xstate";
import {onMounted} from "vue"
import HelloWorld from "./components/HelloWorld.vue"
export default {
    name: "App",
    components: {HelloWorld},
    setup() {
        
    }
};
</script>