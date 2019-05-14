<template>
    <div class="draggable-box" v-drag="true" ref="draggableBoxRef">
        <div class="resizeable-box" :class="resizeBoxClass" :style="resizeBoxStyle" @mouseup="resizeEnd" @mousedown="resizeStart">
            <div v-if="expand" class="slot-box">
                <slot></slot>
            </div>
            <i class="icon-minus" v-if="collapsable && expand" @mouseup="expandIconMouseUp" @mousedown="expandIconMouseDown" @click="toggleExpand"
                title="隐藏">-</i>
            <i class="icon-plus" v-if="collapsable && !expand" @mouseup="expandIconMouseUp" @mousedown="expandIconMouseDown" @click="toggleExpand"
                title="展开">+</i>
        </div>
    </div>
</template>

<script>
// 获取在限定矩形范围内的坐标值
const getPositionWithinBoundary = function(x, y, maxX, maxY) {
    if (x < 0) x = 0;
    if (x > maxX) x = maxX;

    if (y < 0) y = 0;
    if (y > maxY) y = maxY;
    return {
        x,
        y
    };
};

export default {
    name: "DragAndResizePanel",
    props: {
        draggable: {
            type: Boolean,
            default: true
        },
        resizeable: {
            type: Boolean | String, // true | false | 'both' | 'horizontal' | 'vertical'
            default: true
        },
        collapsable: {
            type: Boolean,
            default: true
        },
        width: {
            type: Number
        },
        height: {
            type: Number
        },
        maxWidth: {
            type: Number
        },
        maxHeight: {
            type: Number
        },
        minWidth: {
            type: Number
        },
        minHeight: {
            type: Number
        }
    },
    computed: {
        resizeBoxClass: function() {
            return {
                resizeable: this.resizeable,
                "resizeable-x": this.resizeable === "horizontal",
                "resizeable-y": this.resizeable === "vertical",
                expand: this.expand,
                collapse: !this.expand
            };
        },
        // panel 默认宽高 100 * 100, 最小宽高为 36 * 36 , 最大宽高为 viewport
        resizeBoxStyle: function() {
            if (this.expand) {
                return {
                    height: (this.height || 100) + "px",
                    width: (this.width || 100) + "px",
                    "min-width": Math.max(36, this.minWidth) + "px",
                    "min-height": Math.max(36, this.minHeight) + "px",
                    "max-width": this.maxWidth ? this.maxWidth + "px" : "",
                    "max-height": this.maxHeight ? this.maxHeight + "px" : "",
                    resize: this.expand
                        ? this.resizeable
                            ? this.resizeable === true
                                ? "both"
                                : this.resizeable
                            : "none"
                        : "none"
                };
            } else {
                return {
                    height: "36px",
                    width: "36px"
                };
            }
        }
    },
    data() {
        return {
            // 面板是否展开
            expand: true,
            // 面板展开宽度
            expandWidth: 0,
            // 触发 mousedown 事件的时间
            expandIconMouseDownStart: 0,
            // 面板是否触发 drag 事件
            triggeredDrag: false
        };
    },
    methods: {
        // 切换panel展开状态
        toggleExpand() {
            // panel处于拖拽中，不切换面板展开状态
            if (this.triggeredDrag) {
                return;
            }
            const el = this.$refs.draggableBoxRef;
            const distance = 36;
            if (this.expand) {
                this.expandWidth = el.clientWidth;
                el.style.left =
                    el.offsetLeft + (this.expandWidth - distance) + "px";
            } else {
                el.style.left =
                    Math.max(0, el.offsetLeft - (this.expandWidth - distance)) +
                    "px";
            }
            this.expand = !this.expand;
            this.$emit("toggle-expand", this.expand);
        },
        // panel 鼠标放开
        expandIconMouseUp() {
            const end = Date.now();
            const gap = end - this.expandIconMouseDownStart;
            if (gap > 150) {
                this.triggeredDrag = true;
            }
        },
        // panel 鼠标按下
        expandIconMouseDown() {
            this.triggeredDrag = false;
            this.expandIconMouseDownStart = Date.now();
        },
        // 鼠标出现在右下角 20 * 20px 区域，判定为resize
        resizeStart(event) {
            this.speedTriggeredResize =
                event.currentTarget.clientWidth - event.offsetX <= 20 &&
                event.currentTarget.clientHeight - event.offsetY <= 20;
            if (this.speedTriggeredResize) {
                // 阻止事件冒泡，避免触发drag
                event.stopPropagation();
            }
        },
        resizeEnd(event) {
            if (this.speedTriggeredResize) {
                // 阻止事件冒泡，避免触发drag
                event.stopPropagation();
            }
            this.speedTriggeredResize = false;
        }
    },
    components: {},
    directives: {
        drag: {
            // 指令的定义
            inserted: (el, { value, modifiers }) => {
                //el代表使用该指令的元素
                el.onmousedown = e => {
                    // 鼠标到目标DOM元素内边距的距离
                    var disx = e.offsetX;
                    var disy = e.offsetY;

                    // 目标DOM元素到父元素的距离
                    const offsetX = el.offsetLeft;
                    const offsetY = el.offsetTop;

                    // 父元素到视窗的距离
                    const parentX = e.pageX - disx - offsetX;
                    const parentY = e.pageY - disy - offsetY;

                    //阻止浏览器的默认事件
                    e.preventDefault();
                    document.onmousemove = e => {
                        // 根据鼠标当前位置，重新设置 目标DOM元素到父元素的距离
                        var currX = e.clientX - disx - parentX;
                        var currY = e.clientY - disy - parentY;

                        const maxX =
                            el.offsetParent.clientWidth - el.clientWidth;
                        const maxY =
                            el.offsetParent.clientHeight - el.clientHeight;

                        // 边界控制
                        const { x, y } = getPositionWithinBoundary(
                            currX,
                            currY,
                            maxX,
                            maxY
                        );
                        //如果表达式的结果是false,就不拖拽
                        if (!value) {
                            return;
                        }
                        //修饰符
                        if (modifiers.x) {
                            el.style.left = x + "px";
                        }
                        //修饰符
                        if (modifiers.y) {
                            el.style.top = y + "px";
                        }
                        if (!(modifiers.x && !modifiers.y) && value) {
                            el.style.left = x + "px";
                            el.style.top = y + "px";
                        }
                    };
                    document.onmouseup = function() {
                        document.onmousemove = null;
                        document.onmouseup = null;
                    };
                };
            }
        }
    }
};
</script>

<style lang="less" scoped>
.draggable-box {
    z-index: 99;
    width: auto;
    height: auto;
    position: absolute;
    top: 0px;
    left: 0px;
    &:hover {
        opacity: 1;
        cursor: -webkit-grabbing;
    }
    .resizeable-box {
        // for chrome
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-thumb {
            background: #ccc;
        }
        ::-webkit-scrollbar-track {
            background: none;
        }
        box-sizing: border-box;
        padding-bottom: 15px;
        border: 1px solid rgba(128, 128, 128, 0.37);
        border-radius: 5px;
        background-color: white;
        width: auto;
        height: auto;
        min-width: 36px;
        min-height: 36px;
        max-width: 100vw;
        max-height: 100vh;
        overflow: auto;
        resize: both;
        // panel collapse state
        &.collapse {
            padding-bottom: 0px;
            border: 0px;
            resize: none;
            width: 36px;
            height: 36px;
            min-width: 36px;
            min-height: 36px;
            max-width: 36px;
            max-height: 36px;
            border-radius: 18px;
        }
        .icon-minus,
        .icon-plus {
            box-sizing: border-box;
            width: 36px;
            height: 36px;
            border-radius: 18px;
            position: absolute;
            top: 0px;
            right: 18px;
            font-style: normal;
            font-size: 24px;
            color: gray;
            text-align: center;
            vertical-align: baseline;
            user-select: none;
            &:hover {
                cursor: pointer;
                color: #409eff;
            }
        }
        .icon-minus {
            background-color: #ffffff30;
        }
        .icon-plus {
            background-color: #b7ef9c;
        }
        // slot content
        .slot-box {
            width: 100%;
            height: 100%;
            position: relative;
        }
        // resize cursor tip
        &.resizeable::before {
            content: "";
            position: absolute;
            bottom: 0px;
            right: 0px;
            cursor: se-resize;
            width: 20px;
            height: 20px;
            opacity: 0;
        }
        &.resizeable-x::before {
            cursor: ew-resize;
        }
        &.resizeable-y::before {
            cursor: ns-resize;
        }
    }
}
</style>
