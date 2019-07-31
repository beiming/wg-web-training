<template>
    <div class="ling-legend">
        <div class="top">
            <span class="name">{{legendData.name}}</span>
            <fluctuate :value="legendData.fluctuate"></fluctuate>
        </div>
        <span class="value">{{legendData.summaryCount}}</span>
    </div>
</template>

<script>
    import Fluctuate from "./Fluctuate";

    export default {
        name: "LineLegend",
        components: {Fluctuate},
        props: ['legendData']
    }
</script>

<style scoped lang="scss">
    $colors: #f5a623, #4892f6, #00bbbd;
    @mixin mark($len, $colors) {
        @for $i from 1 through $len {

            &:nth-child(#{$i}) .top:before {
                background: nth($colors, $i);
            }
        }
    }

    .ling-legend {
        display: flex;
        flex-direction: column;

        @include mark(3, $colors);

        .top {
            display: flex;
            align-items: center;

            &:before {
                width: 1.2rem;
                height: 1.2rem;
                background: #f5a623;
                border-radius: 0.6rem;
                content: ' ';
            }

            .name {
                font-size: 1.4rem;
                color: #333333;
                margin-left: 0.6rem;
                margin-right: 1.24rem;
            }
        }

        .value {
            font-size: 2.4rem;
            font-weight: 500;
            color: #333333;
            margin-top: 0.8rem;
        }
    }
</style>
