<style scoped>
    .absolute{
        position:absolute;
    }
    .border{
        border:1px solid #4395ff;
    }
    .rect{
        border:1px solid #4395ff;
        position:relative;
        left:0;
        right:0;
        top:0;
        bottom:0;
        width:100%;
        height:100%;
    }
    .corner{
        width:5px;
        height:5px;
        background:#4395ff;
    }
    .left-top{left:-5px;top:-5px;cursor:nw-resize;}
    .left-bottom{left:-5px;bottom:-5px;cursor:ne-resize;}
    .right-top{right:-5px;top:-5px;cursor:sw-resize;}
    .right-bottom{right:-5px;bottom:-5px; cursor:se-resize;}
</style>

<template>
    <div class="root absolute" ref="root" v-show="visible" :style="{width: width + 'px', height: height + 'px', left: left + 'px', top: top + 'px'}">
        <div class="rect">
            <div class="absolute border corner left-top" ref="left-top-handler" @mousedown="dragStart"></div>
            <div class="absolute border corner right-top" ref="right-top-handler" @mousedown="dragStart"></div>
            <div class="absolute border corner left-bottom" ref="left-bottom-handler" @mousedown="dragStart"></div>
            <div class="absolute border corner right-bottom" ref="right-bottom-handler" @mousedown="dragStart"></div>
        </div>
    </div>
</template>

<script>
export default {
    data () {
        return {
            visible: false,
            width: 0,
            height: 0,
            left: 0,
            top: 0,
            target: null,
        };
    },
    props: ['container'],
    mounted () {
        setTimeout(() => {
        this.container.on('click', (e) => {
            let tar = e.target;
            this.target = tar;
            let pro = tar.getBoundingClientRect();
            if(tar.tagName.toLowerCase() == 'img'){
                let size = [tar.width, tar.height];
                let point = [pro.left + document.documentElement.scrollLeft, pro.top + document.documentElement.scrollTop];
                this.width = size[0];
                this.height = size[1];
                this.left = point[0];
                this.top = point[1];
                this.visible = true;
            }else{
                this.visible = false;
            }
        });
        }, 1000);
    },
    methods: {
        dragStart (e) {
            let origin = [e.clientX, e.clientY];
            let rate = this.height / this.width;

            document.onmousemove = (e) => {
                let current = [e.clientX, e.clientY];
                let dissX = current[0] - origin[0];
                let width = this.width + dissX;
                let height = rate * width;
                this.width = width;
                this.height = height;
                this.target.style.width = width + 'px';
                this.target.style.height = height + 'px';
                origin = current;
            };

            document.onmouseup = () => {
                document.onmousemove = null;
                document.onmouseup = null;
            };
        }
    },
};
</script>
