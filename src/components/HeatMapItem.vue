<template>
    <span :style="{'background-color': background}"></span>
</template>
<script>
    export default {
        props: ["pageView"],
        computed: {
            background() {
                if (this.pageView <= 2134) {
                    return "rgb(217, 245, 245)"
                } else if (this.pageView > 2134 && this.pageView <= 4268) {
                    return "rgb(153, 228, 229)"
                } else if (this.pageView > 4268 && this.pageView <= 6402) {
                    return "rgb(0, 187, 189)"
                } else if (this.pageView > 6402) {
                    return "rgb(0, 127, 128)"
                }
            }
        }
    }
</script>

<style lang="scss">

</style>
