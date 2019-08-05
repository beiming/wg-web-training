<template>
    <div class="heat-map" v-if="loaded">
        <CardHeader>访问量</CardHeader>
        <div class="content">
            <div class="day-header">
                <span class="item" v-for="dayOfWeek in 7">{{days[dayOfWeek-1]}}</span>
            </div>
            <div class="heat-map-matrix">
                <div class="hour-column" v-for="dayOfWeek in 7">
                    <HeatMapItem :page-view="heatMapMatrix[hour-1][dayOfWeek-1]"
                                 v-for="hour in 24" @mouseenter.native="mouseEnterHandler(dayOfWeek, hour)"
                                 @mouseleave.native="showTooltip=false"
                                 @mousemove.native="computeToolTipPosition"></HeatMapItem>
                </div>
                <div class="hour-header">
                    <div v-for="hour in 24">{{hourTitle(hour-1)}}</div>
                </div>
            </div>
            <HeatMapLegend></HeatMapLegend>
        </div>
        <CardFooter :show-date-selector="true" :show-view-more="true"></CardFooter>
        <HeatMapTooltip :show="showTooltip" :x="pageX" :y="pageY + 20" :day="days[matrixX-1]" :hour="hours[matrixY-1]"
                        :page-view="heatMapMatrix[matrixX-1][matrixY-1]">
        </HeatMapTooltip>
    </div>
</template>

<script>
    import CardHeader from "./CardHeader";
    import Vue from "vue";
    import HeatMapItem from "./HeatMapItem";
    import CardFooter from "./CardFooter";
    import HeatMapLegend from "./HeatMapLegend";
    import HeatMapTooltip from "./HeatMapTooltip";

    const DAYS = ["周一", "周二", "周三", "周四", "周五", "周六", "周日"];
    const HOURS = ["00:00", "01:00", "02:00", "03:00", "04:00", "05:00", "06:00", "07:00", "08:00", "09:00", "10:00", "11:00", "12:00", "", "14:00", "", "16:00", "", "18:00", "", "20:00", "", "22:00", ""];

    export default {
        components: { HeatMapTooltip, CardFooter, HeatMapItem, CardHeader, HeatMapLegend },
        data() {
            return {
                loaded: false,
                heatMapMatrix: new Array(24),
                days: DAYS,
                hours: HOURS,
                showTooltip: false,
                pageX: 0,
                pageY: 0,
                matrixX: 1,
                matrixY: 1
            }
        },
        created() {
            this.fetchHeatMapData().then(data => {
                this.parseToHeatMapMatrix(data.result.data[0].rows)
            })
        },
        computed: {
            tooltipPosition() {
                return {
                    x: this.pageX,
                    y: this.pageY + 20
                }
            }
        },
        methods: {
            fetchHeatMapData() {
                let requestBody = {
                    "date_ranges": [{ "start": "2018-09-01", "end": "2018-12-31" }],
                    "dimensions": [{ "name": "day-of-week" }, { "name": "hour" }],
                    "metrics": [{ "name": "tc:page_view" }],
                    "filters": {}
                };
                const option = {
                    body: JSON.stringify(requestBody),
                    headers: {
                        'content-type': 'application/json'
                    },
                    method: 'POST',
                    mode: 'cors',
                };
                return fetch("http://dashboard.qa.tronclass.com.cn/api/al/heatmap", option)
                .then(response => response.json());
            },
            parseToHeatMapMatrix(data) {
                data.forEach(d => {
                    if (!this.heatMapMatrix[d[1]]) {
                        Vue.set(this.heatMapMatrix, d[1], new Array(7));
                    }
                    Vue.set(this.heatMapMatrix[d[1]], d[0], d[2]);
                });
                console.log(this.heatMapMatrix)
                this.loaded = true;
            },
            computeToolTipPosition(event) {
                this.pageX = event.pageX;
                this.pageY = event.pageY
            },
            mouseEnterHandler(x, y) {
                this.matrixX = x;
                this.matrixY = y;
                this.showTooltip = true;
            },
            hourTitle(hour) {
                if (hour % 2 === 0) {
                    return this.hours[hour]
                }
            }
        }
    }
</script>

<style lang="scss">
    .heat-map {
        display: flex;
        flex-direction: column;
        width: 100%;
        height: 100%;
    }

    .content {
        display: flex;
        flex-grow: 1;
        flex-direction: column;
        margin: 0 2rem;

        .day-header {
            display: flex;
            margin-top: 1.6rem;
            margin-bottom: 0.5rem;
            margin-right: 5rem;
            font-size: 1.2rem;
            color: #737373;

            span {
                height: 17px;
                flex-grow: 1;
                text-align: center;
            }
        }

        .hour-header {
            display: flex;
            flex-direction: column;
            color: #737373;
            font-size: 1.2rem;

            div {
                flex-grow: 1;
                width: 5rem;
                margin-bottom: 0.6rem;
                display: flex;
                align-items: center;
                line-height: 0;
            }
        }

        .heat-map-matrix {
            display: flex;
            flex-direction: row;
            flex-grow: 1;

            .hour-column {
                display: flex;
                flex-direction: column;
                flex-grow: 1;
            }
        }
    }
</style>
