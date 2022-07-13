<template>
    <a href="https://stately.ai/viz/2ac5915f-789a-493f-86d3-a8ec079773f4">查看状态</a>
    <br>
    <!-- <state-1 />
    <state-2 />
    <state-3 />
    <state-4 />
    <br>
    <state-5 />
    <state-6  />
    <state-7  />
    <state-8  />
    <state-9  />
    <state-10  />
    <state-11  />
    <state-12  />
    <state-13  />
    <state-14  />
    <state-15  />
    <state-16  />
    <state-17  />
    <state-18  /> -->
    <state-current />
</template>

<script setup>
import State1 from "./1.basic/1.vue"
import State2 from "./1.basic/2.vue"
import State3 from "./1.basic/3.vue"
import State4 from "./1.basic/4.vue"

import State5 from "./2/1.state.vue"
import State6 from "./2/2.event.vue"
import State7 from "./2/3.transition.vue"
import State8 from "./2/4.actions-send.vue"
import State9 from "./2/4.actions-raise.vue"
import State10 from "./2/4.actions-forward.vue"
import State11 from "./2/4.actions-respond.vue"
import State12 from "./2/4.actions-error.vue"
import State13 from "./2/4.actions-choose.vue"

import State14 from "./2/5.guard-cond.vue"
import State15 from "./2/5.guard-custom.vue"

import State16 from "./2/6.context-assign.vue"
import State17 from "./2/7.model.vue"
import State18 from "./2/8.activities.vue"

import State19 from "./3/1.service.invoke.vue"
import State20 from "./3/2.service.callback.vue"
import State21 from "./3/3.service.callbackReceive.vue"
import State22 from "./3/4.service.machine.vue"
import State23 from "./3/5.service.send.vue"
import State24 from "./3/6.service.respond.vue"
import State25 from "./3/7.service.multiple.vue"

import State26 from "./4/1.actors-machine.vue"
import State27 from "./4/2.actors-cb.vue"
import State28 from "./4/3.actors-promise.vue"
import State29 from "./4/4.actors-stop.vue"
import State30 from "./4/5.actors-state.vue"

import State31 from "./5/1.delay.vue"
import State32 from "./5/2.delay-config.vue"
import StateCurrent from "./5/3.delay-cancel.vue"
</script>