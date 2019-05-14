<template>
    <div id="app">
        <drag-and-resize-panel :style="{color:'red'}" :width="150" :height="120" :max-width="300" :min-height="100" :max-height="300" :resizeable="'vertical'" :collapsable="true">
            <div>哈哈哈哈sdfasfsadfsadfsadfsadfsafsadddddddddddddddddddddafsdafsadf哈</div>
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
    computed: {
    }
};
</script>


<style lang="less" scoped>
    #app {
        position:absolute;
        width: 100vw;
        height: 100vh;
    }    
</style>
