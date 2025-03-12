<template>
    <div class="trading-vue-legend title-legend" :style="calc_style">
        <div v-if="(grid_id === 0 && showTitleChartLegend)" class="trading-vue-ohlcv"
            :style="{ 'max-width': common.width + 'px' }">
            <template v-if="common?.showLegendPropsData && common.showLegendPropsData.length">
                <b v-for="(n, i) in common.showLegendPropsData" :key="i">{{ n.k }} : {{ n.v }}&nbsp;</b><br />
            </template>
            <template v-if="show_CustomProps"><span v-for="(n, i) in legendTxtConfig" :key="i"
                    :style="n.style">{{ n.name }}&nbsp;</span></template>
            <span class="t-vue-title" v-if="!show_CustomProps" :style="{ color: common.colors.title }">
                {{ common.title_txt }}
            </span>
            <span class="t-vue-exchange">
                {{ common.exchange_txt }}
            </span>
            <span v-if="show_values && !show_CustomProps">
                O<span class="t-vue-lspan">{{ values }}</span>
                <!-- H<span class="t-vue-lspan">{{ ohlcv[1] }}</span>
                L<span class="t-vue-lspan">{{ ohlcv[2] }}</span>
                C<span class="t-vue-lspan">{{ ohlcv[3] }}</span>
                V<span class="t-vue-lspan">{{ ohlcv[4] }}</span> -->
            </span>
            <span v-if="!show_values" class="t-vue-lspan" :style="{ color: common.colors.text }">
                {{ (common.meta.last || [])[4] }}
            </span>
        </div>
        <!-- {{ values }} -->

    </div>
</template>
<script>

export default {
    name: 'TitleChartLegend',
    components: { },
    props: [
        'common', 'values', 'decimalPlace', 'grid_id', 'meta_props', 'legendDecimal', 'showTitleChartLegend',
    ],
    data() {
        return {
            open: 'n/a'
        }
    },
    watch:{
        ohlcv:{
            handler:function(){
                this.open = this.ohlcv[0]
            },
            deep:true
        }
    },
    computed: {
        show_CustomProps() {
            return this.common?.show_CustomProps || false;
        },
        legendTxtConfig() {
            return this.common?.legendTxtConfig;
        },
        ohlcv() {
            console.log('this.$props.values', this.$props.values);
            
            if (!this.$props.values || !this.$props.values.ohlcv) {
                return Array(6).fill('n/a')
            }
            // const prec = this.layout.prec
            const prec = this.decimalPlace
            // const prec = 3
            // TODO: main the main legend more customizable
            let id = this.main_type + '_0'
            let meta = this.$props.meta_props[id] || {}
            if (meta.legend) {
                return (meta.legend() || []).map(x => x.value)
            }

            if (this.$props.legendDecimal) {
                return [
                    this.$props.values.ohlcv[1].toFixed(this.$props.values.ohlcv[1] < 1 ? 3 : 2),
                    this.$props.values.ohlcv[2].toFixed(this.$props.values.ohlcv[2] < 1 ? 3 : 2),
                    this.$props.values.ohlcv[3].toFixed(this.$props.values.ohlcv[3] < 1 ? 3 : 2),
                    this.$props.values.ohlcv[4].toFixed(this.$props.values.ohlcv[4] < 1 ? 3 : 2),
                    this.$props.values.ohlcv[5] ?
                        Number(this.$props.values.ohlcv[5].toFixed(0)).toLocaleString('en-AU') :
                        'n/a'
                ]
            } else {
                return [
                    this.$props.values.ohlcv[1].toFixed(prec),
                    this.$props.values.ohlcv[2].toFixed(prec),
                    this.$props.values.ohlcv[3].toFixed(prec),
                    this.$props.values.ohlcv[4].toFixed(prec),
                    this.$props.values.ohlcv[5] ?
                        Number(this.$props.values.ohlcv[5].toFixed(0)).toLocaleString('en-AU') :
                        'n/a'
                ]
            }
        },
        calc_style() {
            let top = this.layout.height > 150 ? 3 : 1
            let grids = this.$props.common.layout.grids
            let w = grids[0] ? grids[0].width : undefined
            return {
                top: `${this.layout.offset + top}px`,
                width: `${w - 20}px`
            }
        },
        layout() {
            const id = this.$props.grid_id
            return this.$props.common.layout.grids[id]
        },
        main_type() {
            let f = this.common.data.find(x => x.main)
            return f ? f.type : undefined
        },
        show_values() {
            return this.common.cursor.mode !== 'explore'
        }
    },
    methods: {
    }
}
</script>
<style>
.trading-vue-legend {
    position: relative;
    z-index: 1;
    font-size: 1.25em;
    margin-left: 10px;
    pointer-events: none;
    text-align: left;
    user-select: none;
    font-weight: 300;
}

@media (min-resolution: 2x) {
    .trading-vue-legend {
        font-weight: 400;
    }
}

.trading-vue-ohlcv {
    pointer-events: none;
    margin-bottom: 0.5em;
}

.t-vue-lspan {
    font-variant-numeric: tabular-nums;
    font-size: 0.95em;
    color: #999999;
    /* TODO: move => params */
    margin-left: 0.1em;
    margin-right: 0.2em;
}

.t-vue-title {
    font-size: 1.45em;
}

.t-vue-exchange{
    font-size: 0.9em;
    font-weight: 600;
    margin-right: 0.25em;
}

.t-vue-ind {
    display: flex;
    margin-left: 0.2em;
    margin-bottom: 0.5em;
    font-size: 1.0em;
    margin-top: 0.3em;
}

.t-vue-ivalue {
    margin-left: 0.5em;
}

.t-vue-unknown {
    color: #999999;
    /* TODO: move => params */
}

.tvjs-appear-enter-active,
.tvjs-appear-leave-active {
    transition: all .25s ease;
}

.tvjs-appear-enter,
.tvjs-appear-leave-to {
    opacity: 0;
}
</style>
