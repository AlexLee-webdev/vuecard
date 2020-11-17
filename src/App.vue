<template>
    <div id="app">
        <button style="margin-left: 100px;" @click='addLists()'>Create a panel</button>
        <button style="margin-left: 100px;" @click="alignLists()">Align the panels</button>
        <div class="list" id="list">
            <li>
                <VueDragResize v-for="(rect, index) in rects"
                            :key="index"
                            :w="rect.width"
                            :h="rect.height"
                            :x="rect.left"
                            :y="rect.top"
                            :parentW="listWidth"
                            :parentH="listHeight"
                            :axis="rect.axis"
                            :isActive="rect.active"
                            :minw="rect.minw"
                            :minh="rect.minh"
                            :isDraggable="rect.draggable"
                            :isResizable="rect.resizable"
                            :parentLimitation="rect.parentLim"
                            :snapToGrid="rect.snapToGrid"
                            :aspectRatio="rect.aspectRatio"
                            :z="rect.zIndex"
                            :stickSize="5"
                            :contentClass="rect.class"
                            v-on:activated="activateEv(index)"
                            v-on:deactivated="deactivateEv(index)"
                            v-on:dragging="changePosition($event, index)"
                            v-on:resizing="changeSize($event, index)"
                >
                    <div class="filler" :style="{backgroundColor:rect.color}">
                        <button @click="removeTheList(index)">X</button>
                    </div>
                </VueDragResize>
            </li>
        </div>
    </div>
</template>

<style>
    body {
        height: 100vh;
        width: 100vw;
        background-color: #ECECEC;
    }

    #app {
        margin: 0;
        box-sizing: border-box;
        width: 100%;
        height: 100%;
        position: relative;
        font-family: 'Lato', sans-serif;
    }

    .filler {
        width: 100%;
        height: 100%;
        display: inline-block;
        position: absolute;
    }

    .list {
        position: absolute;
        top: 50px;
        bottom: 30px;
        left: 30px;
        right: 30px;
        box-shadow: 0 0 2px #AAA;
        background-color: white;
        display: inline-block;
    }

    .box-shaddow {
        box-shadow:  10px 10px 15px 0px rgba(125,125,125,1);
    }
</style>

<script>
    import VueDragResize from './components/vue-drag-resize/vue-drag-resize.vue';
    import lists from './store/randomList';
    import './icons';

    export default {
        name: 'App',

        components: {
            VueDragResize,
        },

        data(){
            return {
                listWidth: 0,
                listHeight: 0,
            }
        },

        mounted() {
            let listEl = document.getElementById('list');
            this.listWidth = listEl.clientWidth;
            this.listHeight = listEl.clientHeight;

            window.addEventListener('resize', ()=>{
                this.listWidth = listEl.clientWidth;
                this.listHeight = listEl.clientHeight;
            });
        },

        computed: {
            rects: function() {
                return this.$store.state.rect.rects;
            },

        },

        methods: {
            addLists() {
                var A_List = {};
                A_List = lists();
                this.$store.dispatch('rect/addAList', {A_List});
            },
 
            alignLists() {
                const rects = this.$store.state.rect.rects;
                const customziedWidth = this.listWidth / 2 -60;
                const customziedHeight = 100;
                var topEven = 10;
                var topOdd = 10;
                var left = 10;

                for (let index = 0; index < rects.length; index++) {
                    if(index % 2 === 0){
                        left = 10;
                        this.$store.dispatch('rect/setTop', {id: index, top: topEven});
                        topEven += (rects[index].height + 10);
                    } else {
                        left = customziedWidth + 20;
                        this.$store.dispatch('rect/setTop', {id: index, top: topOdd});
                        topOdd += (rects[index].height + 10);
                    }
                    this.$store.dispatch('rect/setLeft', {id: index, left: left});
                    this.$store.dispatch('rect/setWidth', {id: index, width: customziedWidth});
                    this.$store.dispatch('rect/setHeight', {id: index, height: customziedHeight});
                }
            },
            removeTheList(index) {
                this.$store.dispatch('rect/removeTheList', {id: index});
            },
            activateEv(index) {
                this.$store.dispatch('rect/setActive', {id: index});
            },

            deactivateEv(index) {
                this.$store.dispatch('rect/unsetActive', {id: index});
            },

            changePosition(newRect, index) {

                this.$store.dispatch('rect/setTop', {id: index, top: newRect.top});
                this.$store.dispatch('rect/setLeft', {id: index, left: newRect.left});
                this.$store.dispatch('rect/setWidth', {id: index, width: newRect.width});
                this.$store.dispatch('rect/setHeight', {id: index, height: newRect.height});
            },

            changeSize(newRect, index) {
                this.$store.dispatch('rect/setTop', {id: index, top: newRect.top});
                this.$store.dispatch('rect/setLeft', {id: index, left: newRect.left});
                this.$store.dispatch('rect/setWidth', {id: index, width: newRect.width});
                this.$store.dispatch('rect/setHeight', {id: index, height: newRect.height});
            }
        }
    }
</script>