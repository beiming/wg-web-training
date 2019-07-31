<template>
    <div class="page-view">
        <card-header>访问分析</card-header>
        <div class="content">
            <div class="tabs">
                <Tab v-for="data in chartsData"
                     :key="data.metric"
                     :tab-data="{title: data.metricName, value: data.total, fluctuate: data.fluctuate, tabKey: data.metric}"
                     v-on:update:selected-tab="onSelectTab"
                     v-bind:selected-tab.sync="selectedTab"></Tab>
            </div>
            <div class="chart-container">
                <e-charts :options="defaultOptions" ref="chart" autoresize></e-charts>
            </div>
            <div class="line-legend-container">
                <line-legend v-for="legend in currentChartData.customLegends"
                             :key="legend.name"
                             :legend-data="legend"></line-legend>
            </div>
        </div>
        <card-footer :show-date-selector="true" :show-view-more="true"></card-footer>
    </div>
</template>

<script>
    import ECharts from "vue-echarts";
    import "echarts";
    import {getPageViewData, lineChartDefaultOptions} from "./pageViewUtils";
    import CardFooter from "./CardFooter";
    import CardHeader from "./CardHeader";
    import Tab from "./Tab";
    import LineLegend from "./LineLegend";

    export default {
        name: "PageView",
        components: {LineLegend, Tab, ECharts, CardHeader, CardFooter},

        data: function () {
            return {
                defaultOptions: lineChartDefaultOptions,
                chartsData: undefined,
                selectedTab: undefined
            }
        },

        created() {
        },

        mounted() {
            this.fetchData();
        },

        methods: {
            fetchData: function () {
                this.$refs.chart.showLoading();

                getPageViewData()
                    .then(this.onFetchDataSuccess)
                    .catch(this.onFetchDataFailure);
            },
            onFetchDataFailure: function (error) {
                this.$refs.chart.hideLoading();
            },

            onFetchDataSuccess: function (chartsData) {
                // TODO: look here: data structure
                console.log('--------- look here: data structure ---------');
                console.log(chartsData);
                console.log('--------- look here: data structure ---------');

                this.chartsData = chartsData;

                if (!this.selectedTab) {
                    this.selectedTab = chartsData[0].metric;
                    this.$refs.chart.mergeOptions(chartsData[0].options);
                }

                this.$refs.chart.hideLoading();
            },
            getCurrentChartData: function () {
                if (!this.chartsData) {
                    return {};
                }
                let chartData = this.chartsData.find(chart => chart.metric === this.selectedTab);
                if (chartData) {
                    return chartData;
                }
                return {}
            },
            onSelectTab: function (tabKey) {
                this.selectedTab = tabKey;
                this.$refs.chart.mergeOptions(this.getCurrentChartData().options);
            }
        },

        computed: {
            currentChartData: function () {
                return this.getCurrentChartData();
            }
        }
    }
</script>

<style scoped lang="scss">
    .page-view {
        display: flex;
        flex-direction: column;
        width: 100%;
        height: 100%;

        .content {
            position: relative;
            flex: 1;
            display: flex;
            flex-direction: column;
            padding-bottom: 2rem;

            .tabs {
                display: flex;
                margin-bottom: 1.5rem;

                > div {
                    flex: 1;
                }
            }

            .chart-container {
                position: relative;
                flex: 1;
                padding: 0 2rem 0 1rem;

                .echarts {
                    width: 100%;
                    height: 100%;
                }

            }

            .line-legend-container {
                display: flex;
                justify-content: space-around;
                margin-top: 2rem;
            }
        }

    }
</style>

