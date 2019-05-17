<template>
    <div id="app">
        <drag-and-resize-panel :style="{color:'red'}" :max-width="300" :max-height="300"
            :resizeable="true" :collapsable="true" @toggle-expand="eventHandel('toggle-expand', $event)">
            <div>
                <span>panel 内容</span>
                <img src="/asserts/prerender_broswer.PNG" alt="">
            </div>
        </drag-and-resize-panel>
    </div>
</template>

<script>
import DragAndResizePanel from "./DragAndResizePanel";

export default {
    name: "app",
    data() {
        return {};
    },
    components: {
        "drag-and-resize-panel": DragAndResizePanel
    },
    computed: {},
    methods: {
        eventHandel(eventName, params) {
            console.log(`event ${eventName} trigged, params is :`);
            console.table(params);
        }
    }
};
</script>


<style lang="less" scoped>
#app {
    position: absolute;
    width: 100vw;
    height: 100vh;
}
</style>
