<template>
    <a href="https://stately.ai/viz/2ac5915f-789a-493f-86d3-a8ec079773f4">查看状态</a>
    <br>
    <state-1 v-show="false" />
    <state-2 v-show="false" />
    <state-3 v-show="false" />
    <state-4 v-show="false" />
    <br>
    <state-5 v-show="true" />
</template>

<script setup>
import State1 from "./1.basic/1.vue"
import State2 from "./1.basic/2.vue"
import State3 from "./1.basic/3.vue"
import State4 from "./1.basic/4.vue"

import State5 from "./2/1.vue"
</script>