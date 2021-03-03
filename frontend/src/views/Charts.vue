<template>
    <div>
        <input-box title="Генерація Діаграм">
            <form @submit.prevent="generateChart">
                <div class="form-group">
                    <b-form-select v-model="newChartData.chartType" :options="chartTypes"></b-form-select>
                    <small class="form-text text-muted">Вибрати тип діаграми</small>
                </div>
                <b-form-checkbox v-model="newChartData.allCategories" name="checkbox-1" class="mb-3">
                    Вибрати всі категорії
                </b-form-checkbox>
                <div class="form-group">
                    <b-form-select :disabled="newChartData.allCategories" multiple placeholder="Categories"
                                   v-model="newChartData.categories"
                                   :options="$store.getters.getCategoriesOptions"></b-form-select>
                    <small class="form-text text-muted">
                        Утримуючи кнопку Ctrl (Windows) / Command (Mac), виберіть кілька категорій
                    </small>
                </div>
                <div class="form-group" v-if="newChartData.chartType === 'line'">
                    <b-form-select v-model="newChartData.transactionType" :options="transactionTypes"></b-form-select>
                    <small class="form-text text-muted">Виберіть тип оплати</small>
                </div>
                <div class="row">
                    <div class="col-lg-6" v-if="newChartData.chartType === 'line'">
                        <datetime placeholder="З XX/XX/XXXX" v-model="newChartData.dateFrom" type="date" class="mb-3"
                                  input-class="form-control mb-3"></datetime>
                    </div>
                    <div class="col-lg-6" v-if="newChartData.chartType === 'line'">
                        <datetime placeholder="По XX/XX/XXXX" v-model="newChartData.dateTo" type="date" class="mb-3"
                                  input-class="form-control mb-3"></datetime>
                    </div>
                </div>
                <button-component>Генерувати</button-component>
            </form>
        </input-box>
        <input-box title="Chart">
            <highcharts :options="chartOptions"></highcharts>
        </input-box>
    </div>
</template>

<script>
    import InputBox from '@/components/InputBox.vue'
    import ButtonComponent from '@/components/ButtonComponent.vue'
    import {Chart} from 'highcharts-vue'

    export default {
        name: "Charts",
        components: {InputBox, ButtonComponent, highcharts: Chart},
        data() {
            return {
                datacollection: null,
                newChartData: {
                    transactionType: 'e',
                    chartType: 'pie',
                    allCategories: false,  // for instant select all categories
                    categories: [],
                    dateFrom: null,
                    dateTo: null,
                },
                chartTypes: [
                    {value: 'pie', text: 'Кругова діаграма'},
                    {value: 'line', text: 'Лінійна діаграма'},
                ],
                transactionTypes: [
                    {value: 'e', text: 'Витрати'},
                    {value: 'i', text: 'Прибуток'},
                ],
                chartOptions: {
                    chart: {
                        type: 'line',
                        backgroundColor: '#fbfbfb'
                    },
                    title: {
                        text: 'Діаграма'
                    },
                    series: [{
                        data: []
                    }],
                    xAxis: {
                        type: 'datetime'
                    },
                }
            }
        },
        methods: {
            generateChart() {
                if (this.newChartData.chartType === 'line') {
                    this.$store.dispatch('getLineChart', {
                        transactionType: this.newChartData.transactionType,
                        dateFrom: this.newChartData.dateFrom,
                        dateTo: this.newChartData.dateTo,
                        categories: this.newChartData.categories,
                        allCategories: this.newChartData.allCategories,
                    }).then(data => {
                        let result = []

                        data.forEach((d) => {
                            result.push({x: new Date(d.date).getTime(), y: d.amount, name: d.title})
                        })

                        this.chartOptions.chart.type = 'line'
                        this.chartOptions.series = [{data: result}]
                    })
                } else {
                    this.$store.dispatch('getPieChart', {
                        categories: this.newChartData.categories,
                        allCategories: this.newChartData.allCategories,
                    }).then(data => {
                        this.chartOptions.chart.type = 'pie'
                        console.log(data)
                        this.chartOptions.series = [{data: data, name: 'Categories'}]
                    })
                }
            }
        },
        created() {
            this.$store.dispatch('updateCategories')
            this.$Progress.finish()
        }
    }
</script>

<style scoped>

</style>