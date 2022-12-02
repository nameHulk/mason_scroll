<template>
    <div class="mason-scroll" :id="ID+'scroll'" :ref="ID+'scroll'">
        <div :id="ID" :ref="ID" class="scroll-content" :style="'overflow-x:' + (scrollX?'scroll':'hidden') + ';overflow-y:' + (scrollY?'scroll':'hidden') + ';'" @scroll="scrollEvent">
            <slot></slot>
        </div>
        <div v-if="scrollX && showScrollX && scrolls.canScrollX" class="x-track" :style="xTrackStyle" @mousedown.stop="xScrollTo" @mouseenter.stop="xChangeTrack(true)" @mouseleave.stop="xChangeTrack(false)">
            <div class="x-thumb" @mousedown.stop="xStartMoveTrack" @mouseup.stop="xEndMoveTrack" @mousemove.stop="xMovingTrack" :style="'width:' + scrolls.width + 'px;left:' + scrolls.left + 'px;'"></div>
        </div>
        <div v-if="scrollY && showScrollY && scrolls.canScrollY" class="y-track" :style="yTrackStyle" @mousedown.stop="yScrollTo" @mouseenter.stop="yChangeTrack(true)" @mouseleave.stop="yChangeTrack(false)">
            <div class="y-thumb" @mousedown.stop="yStartMoveTrack" @mouseup.stop="yEndMoveTrack" @mousemove.stop="yMovingTrack" :style="'height:' + scrolls.height + 'px;top:' + scrolls.top + 'px;'"></div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        /* 计算延迟 针对部分容器高度变化增加过渡动画 */
        delay: {
            type: Number,
            default: 500
        },
        /* ID */
        ID: {
            type: String,
            default: 'MasonScroll'
        },
        /* x轴是否滚动 */
        scrollX: {
            type: Boolean,
            default: true
        },
        /* x轴是否显示滚动条 */
        showScrollX: {
            type: Boolean,
            default: true
        },
        /* y轴是否滚动 */
        scrollY: {
            type: Boolean,
            default: true
        },
        /* y轴是否显示滚动条 */
        showScrollY: {
            type: Boolean,
            default: true
        },
    },
    data(){
        return {
            list: 3,
            /* 滚动条 */
            scrolls: {
                canScrollX: false,
                canScrollY: false,
                scrollWidth: 0,
                scrollHeight: 0,
                clientWidth: 0,
                clientHeight: 0,
                width: 0,
                height: 0,
                left: 0,
                top: 0,
                xShowTrack: false,
                xIsMoving: false,
                xMovingStart: 0,
                yShowTrack: false,
                yIsMoving: false,
                yMovingStart: 0,
            },
        }
    },
    computed:{
        /* x轴 - 滚动背景 */
        xTrackStyle(){
            if(this.scrollX && this.scrolls.xShowTrack){
                return 'border-top: 1px solid rgba(255,255,255,0.08);background-color:rgba(255,255,255,0.03)'
            }else{
                return 'border-top: 1px solid transparent'
            }
        },
        /* 滚动条背景 */
        yTrackStyle(){
            if(this.scrollY && this.scrolls.yShowTrack){
                return 'border-left: 1px solid rgba(255,255,255,0.08);background-color:rgba(255,255,255,0.03)'
            }else{
                return 'border-left: 1px solid transparent'
            }
        }
    },
    mounted(){
        setTimeout(() =>{
            this.mathScrolls()
        },300)
        this.$nextTick(() =>{
            this.handleScroll();
            this.handleContainer();
        })
    },
    methods:{
        /* 监听外部容器属性变化 - 动态高度变化 自动重置滚动信息 */
        handleScroll(){
            var _this = this
            var code = this.ID + 'scroll'
            var $tar = document.getElementById(code);
            var option = {
                // childList: true, // 子节点的变动（新增、删除或者更改）
                attributes: true, // 属性的变动
                // characterData: true, // 节点内容或节点文本的变动
                // subtree: true, // 是否将观察器应用于该节点的所有后代节点
                attributeFilter: ['class', 'style'], // 观察特定属性
                // attributeOldValue: true, // 观察 attributes 变动时，是否需要记录变动前的属性值
                // characterDataOldValue: true // 观察 characterData 变动，是否需要记录变动前的值
            }
            var mutationObserver = new MutationObserver(function (mutations) {
                if(mutations){
                    setTimeout(() =>{
                        _this.mathScrolls()
                    },_this.delay)
                }
            })
            mutationObserver.disconnect();
            mutationObserver.observe($tar, option);
        },
        /* 监听文本区域内容变化 - 列表刷新或加载 自动重置滚动信息 */
        handleContainer(){
            var _this = this
            var $tar = document.getElementById(this.ID);
            var option = {
                childList: true, // 子节点的变动（新增、删除或者更改）
                attributes: true, // 属性的变动
                characterData: true, // 节点内容或节点文本的变动
                // subtree: true, // 是否将观察器应用于该节点的所有后代节点
                // attributeFilter: ['class', 'style'], // 观察特定属性
                // attributeOldValue: true, // 观察 attributes 变动时，是否需要记录变动前的属性值
                // characterDataOldValue: true // 观察 characterData 变动，是否需要记录变动前的值
            }
            var mutationObserver = new MutationObserver(function (mutations) {
                if(mutations){
                    _this.mathScrolls()
                }
            })
            mutationObserver.disconnect();
            mutationObserver.observe($tar, option);
        },
        /* 计算滚动区域 */
        mathScrolls(){
            const el = document.getElementById(this.ID)
            // 横轴
            this.scrolls.scrollWidth = el.scrollWidth
            this.scrolls.clientWidth = el.clientWidth
            this.scrolls.width = parseInt(el.clientWidth / el.scrollWidth * el.clientWidth)
            if(this.scrolls.scrollWidth == this.scrolls.clientWidth){
                this.scrolls.canScrollX = false
            }else{
                this.scrolls.canScrollX = true
            }
            // 纵轴
            this.scrolls.scrollHeight = el.scrollHeight
            this.scrolls.clientHeight = el.clientHeight
            this.scrolls.height = parseInt(el.clientHeight / el.scrollHeight * el.clientHeight)
            if(this.scrolls.scrollHeight == this.scrolls.clientHeight){
                this.scrolls.canScrollY = false
            }else{
                this.scrolls.canScrollY = true
            }
            this.scrollEvent();
        },
        /* 区域滚动监听 */
        scrollEvent(){
            const el = document.getElementById(this.ID)
            // 横轴
            var scrollLeft = Math.ceil(el.scrollLeft / this.scrolls.scrollWidth * this.scrolls.clientWidth)
            if(scrollLeft + this.scrolls.width <= this.scrolls.clientWidth){
                this.scrolls.left = scrollLeft
            }
            if(scrollLeft + this.scrolls.width == this.scrolls.clientWidth && this.scrollX){
                this.scrollEnd('x');
            }
            // 纵轴
            var scrollTop = Math.ceil(el.scrollTop / this.scrolls.scrollHeight * this.scrolls.clientHeight)
            if(scrollTop + this.scrolls.height <= this.scrolls.clientHeight){
                this.scrolls.top = scrollTop
            }
            if(scrollTop + this.scrolls.height == this.scrolls.clientHeight && this.scrollY){
                this.scrollEnd('y');
            }
        },
        /* x显示隐藏滚动背景条 */
        xChangeTrack(val){
            this.scrolls.xShowTrack = val
            if(!val){
                this.scrolls.xIsMoving = false
            }
        },
        /* x轴拖动 - 开始 */
        xStartMoveTrack(e){
            this.scrolls.xIsMoving = true
            this.scrolls.xMovingStart = e.offsetX
        },
        /* x轴拖动 - 拖动 */
        xMovingTrack(e){
            if(this.scrolls.xIsMoving){
                if(e.offsetX - this.scrolls.xMovingStart + this.scrolls.left < 0){
                    this.scrolls.left = 0
                }else if(e.offsetX - this.scrolls.xMovingStart + this.scrolls.left + this.scrolls.width < this.scrolls.clientWidth && e.offsetX - this.scrolls.xMovingStart + this.scrolls.left + this.scrolls.width > 0){
                    this.scrolls.left += (e.offsetX - this.scrolls.xMovingStart)
                }else if(e.offsetX - this.scrolls.xMovingStart + this.scrolls.left + this.scrolls.width >= this.scrolls.clientWidth){
                    this.scrolls.left = this.scrolls.clientWidth - this.scrolls.width
                }
                const el = document.getElementById(this.ID)
                el.scrollLeft = this.scrolls.left/this.scrolls.clientWidth*this.scrolls.scrollWidth
            }
        },
        /* x轴 - 快速定位 */
        xScrollTo(e){
            this.scrolls.left = e.offsetX
            const el = document.getElementById(this.ID)
            el.scrollLeft = this.scrolls.left/this.scrolls.clientWidth*this.scrolls.scrollWidth
        },
        /* x轴拖动 - 结束 */
        xEndMoveTrack(e){
            this.scrolls.xIsMoving = false
            this.scrolls.xMovingStart = 0
        },
        /* y显示隐藏滚动背景条 */
        yChangeTrack(val){
            this.scrolls.yShowTrack = val
            if(!val){
                this.scrolls.yIsMoving = false
            }
        },
        /* y轴拖动 - 开始 */
        yStartMoveTrack(e){
            this.scrolls.yIsMoving = true
            this.scrolls.yMovingStart = e.offsetY
        },
        /* y轴拖动 - 拖动 */
        yMovingTrack(e){
            if(this.scrolls.yIsMoving){
                if(e.offsetY - this.scrolls.yMovingStart + this.scrolls.top < 0){
                    this.scrolls.top = 0
                }else if(e.offsetY - this.scrolls.yMovingStart + this.scrolls.top + this.scrolls.height < this.scrolls.clientHeight && e.offsetY - this.scrolls.yMovingStart + this.scrolls.top + this.scrolls.height > 0){
                    this.scrolls.top += (e.offsetY - this.scrolls.yMovingStart)
                }else if(e.offsetY - this.scrolls.yMovingStart + this.scrolls.top + this.scrolls.height >= this.scrolls.clientHeight){
                    this.scrolls.top = this.scrolls.clientHeight - this.scrolls.height
                }
                const el = document.getElementById(this.ID)
                el.scrollTop = Math.ceil(this.scrolls.top/this.scrolls.clientHeight*this.scrolls.scrollHeight)
            }
        },
        /* y轴拖动 - 结束 */
        yEndMoveTrack(e){
            this.scrolls.yIsMoving = false
            this.scrolls.yMovingStart = 0
        },
        /* y轴 - 快速定位 */
        yScrollTo(e){
            this.scrolls.top = e.offsetY
            const el = document.getElementById(this.ID)
            el.scrollTop = Math.ceil(this.scrolls.top/this.scrolls.clientHeight*this.scrolls.scrollHeight)
        },
        /* 滚动到底部 */
        scrollEnd(way){
            if(way == 'x'){
                this.$emit('scrollEndX')
            }else if(way == 'y'){
                this.$emit('scrollEndY')
            }
        },
    }
}
</script>

<style scoped>
    .mason-scroll{
        width: 100%;
        height: 100%;
        background-color: transparent;
        position: relative;
    }
    .mason-scroll .scroll-content{
        width: 100%;
        height: 100%;
        text-align: left;
        position: relative;
        scrollbar-width: none !important;
        -ms-overflow-style: none !important;
    }
    .mason-scroll .scroll-content::-webkit-scrollbar{
        display: none !important;
    }
    .mason-scroll .x-track{
        width: 100%;
        height: 10px;
        position: absolute;
        left: 0px;
        bottom: 0px;
        z-index: 9;
        display: flex;
        flex-direction: row;
        align-items: center;
        transition: all 0.3s;
        -webkit-transition: all 0.3s;
    }
    .mason-scroll .x-track:hover{
        height: 20px;
    }
    .mason-scroll .x-track .x-thumb{
        height: 70%;
        background-color: rgba(255,255,255,0.3);
        border-radius: 999999px;
        position: absolute;
        cursor: pointer;
        opacity: 0;
        transition: opacity 0.3s;
        -webkit-transition: opacity 0.3s;
    }
    .mason-scroll:hover .x-track .x-thumb{
        opacity: 1;
        transition: opacity 0.3s;
        -webkit-transition: opacity 0.3s;
    }
    .mason-scroll .y-track{
        width: 10px;
        height: 100%;
        position: absolute;
        right: 0px;
        top: 0px;
        z-index: 9;
        display: flex;
        flex-direction: column;
        align-items: center;
        transition: all 0.3s;
        -webkit-transition: all 0.3s;
    }
    .mason-scroll .y-track:hover{
        width: 20px;
    }
    .mason-scroll .y-track .y-thumb{
        width: 70%;
        background-color: rgba(255,255,255,0.3);
        border-radius: 999999px;
        position: absolute;
        cursor: pointer;
        opacity: 0;
        transition: opacity 0.3s;
        -webkit-transition: opacity 0.3s;
    }
    .mason-scroll:hover .y-track .y-thumb{
        opacity: 1;
        transition: opacity 0.3s;
        -webkit-transition: opacity 0.3s;
    }
</style>