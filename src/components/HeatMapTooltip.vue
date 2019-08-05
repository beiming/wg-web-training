<template>
    <div class="heat-map-tooltip" v-show="show" :style="{'left': x + 'px', 'top': y + 'px'}">
        <div class="metrics">{{day}}{{hour}}</div>
        <div class="page-view">
            <span>访问量</span>
            <span>{{pageView}}</span>
        </div>
    </div>
</template>

<script>
    export default {
        props: ['show', 'x', 'y', 'day', 'hour', 'pageView']
    }
</script>

<style lang="scss">
    .heat-map-tooltip {
        position: absolute;
        border-style: solid;
        white-space: nowrap;
        z-index: 9999999;
        transition: left 0.4s cubic-bezier(0.23, 1, 0.32, 1) 0s, top 0.4s cubic-bezier(0.23, 1, 0.32, 1) 0s;
        border-width: 0;
        border-color: rgb(51, 51, 51);
        border-radius: 4px;
        color: rgb(115, 115, 115);
        font: 12px/21px sans-serif;
        opacity: 0.8;
        min-width: 144px;
        padding: 12px;
        box-shadow: rgb(232, 232, 232) 0 0 10px;
        background-color: rgb(255, 255, 255);
        pointer-events: none;


        .metrics {
            padding: 2rem 1.5rem;
            font-size: 1.4rem;
        }

        .page-view {
            display: flex;
            align-items: center;
            justify-content: space-between;

            span {
                &:first-child:before {
                    display: inline-block;
                    content: ' ';
                    width: 1.2rem;
                    height: 1.2rem;
                    background: #f5a623;
                    border-radius: 0.6rem;
                }

                &:last-child {

                }
            }
        }
    }
</style>
