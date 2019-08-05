<template>
    <div class="heat-map" v-if="loaded">
        <CardHeader>访问量</CardHeader>
        <div class="content">
            <div class="day-header">
                <span class="item" v-for="dayOfWeek in 7">{{days[dayOfWeek-1]}}</span>
            </div>
            <div class="heat-map-matrix">
                <div class="hour-column" v-for="dayOfWeek in 7">
                    <HeatMapItem :page-view="heatMapMatrix[hour-1][dayOfWeek-1]" v-for="hour in 24"></HeatMapItem>
                </div>
                <div class="hour-header">
                    <div v-for="hour in 24">{{hours[hour-1]}}</div>
                </div>
            </div>
            <HeatMapLegend></HeatMapLegend>
        </div>
        <CardFooter :show-date-selector="true" :show-view-more="true"></CardFooter>
    </div>
</template>

<script>
    import CardHeader from "./CardHeader";
    import Vue from "vue";
    import HeatMapItem from "./HeatMapItem";
    import CardFooter from "./CardFooter";
    import HeatMapLegend from "./HeatMapLegend";

    const DAYS = ["周一", "周二", "周三", "周四", "周五", "周六", "周日"];
    const HOURS = ["00:00", "", "02:00", "", "04:00", "", "06:00", "", "08:00", "", "10:00", "", "12:00", "", "14:00", "", "16:00", "", "18:00", "", "20:00", "", "22:00", ""];

    export default {
        components: { CardFooter, HeatMapItem, CardHeader, HeatMapLegend },
        data() {
            return {
                loaded: false,
                heatMapMatrix: new Array(24),
                days: DAYS,
                hours: HOURS
            }
        },
        created() {
            this.fetchHeatMapData().then(data => {
                this.parseToHeatMapMatrix(data.result.data[0].rows)
            })
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

        .day-header {
            display: flex;
            margin-top: 1.6rem;
            margin-bottom: 0.5rem;
            margin-right: 5rem;

            span {
                height: 17px;
                flex-grow: 1;
                text-align: center;
            }
        }

        .hour-header {
            display: flex;
            flex-direction: column;

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

                span {
                    margin-right: 0.5rem;
                    margin-bottom: 0.6rem;
                    flex-grow: 1;
                }
            }
        }
    }


</style>
