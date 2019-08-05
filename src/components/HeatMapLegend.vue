<template>
    <div class="heat-map-legend">
        <div class="color-group">
            <span style="background: rgb(217, 245, 245)"></span>
            <span style="background: rgb(153, 228, 229)"></span>
            <span style="background: rgb(0, 187, 189)"></span>
            <span style="background: rgb(0, 127, 128)"></span>
        </div>
        <div class="text-group">
            <span>0</span>
            <span>2134</span>
            <span>4268</span>
            <span>6402</span>
            <span>8536</span>
        </div>
    </div>
</template>

<script>
    export default {}
</script>

<style lang="scss">
    .heat-map-legend {
        margin: 1.7rem 5.5rem 1.5rem 0;

        .color-group {
            display: flex;
            height: 1rem;

            span:not(:last-child) {
                margin-right: 0.5rem;
            }

            span {
                flex-grow: 1;
            }
        }

        .text-group {
            display: flex;
            justify-content: space-between;
            font-size: 1.2rem;
            color: #737373;
            margin-top: 0.5rem;

            span:not(:last-child) {
                margin-right: 0.5rem;
            }

            span {
                font-size: 1rem;
            }
        }
    }
</style>
