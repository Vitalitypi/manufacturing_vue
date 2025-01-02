<template>
    <div>
        <el-row :gutter="20" class="mgb20">
            <el-col :span="6">
                <el-card shadow="hover" body-class="card-body">
                    <el-icon class="card-icon bg1">
                        <User />
                    </el-icon>
                    <div class="card-content">
                        <countup class="card-num color1" :end="2134" />
                        <div>用户访问量</div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="6">
                <el-card shadow="hover" body-class="card-body">
                    <el-icon class="card-icon bg2">
                        <ChatDotRound />
                    </el-icon>
                    <div class="card-content">
                        <countup class="card-num color2" :end="168" />
                        <div>系统消息</div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="6">
                <el-card shadow="hover" body-class="card-body">
                    <el-icon class="card-icon bg3">
                        <Goods />
                    </el-icon>
                    <div class="card-content">
                        <countup class="card-num color3" :end="51" />
                        <div>累计高铁站点</div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="6">
                <el-card shadow="hover" body-class="card-body">
                    <el-icon class="card-icon bg4">
                        <ShoppingCartFull />
                    </el-icon>
                    <div class="card-content">
                        <countup class="card-num color4" :end="7" />
                        <div>累计故障总类</div>
                    </div>
                </el-card>
            </el-col>
        </el-row>

        <el-row :gutter="20" class="mgb20">
            <el-col :span="18">
                <el-card shadow="hover">
                    <div class="card-header">
                        <p class="card-header-title">客运动态</p>
                        <p class="card-header-desc">最近一周客运动态，包括高铁站点客运入流量和出流量</p>
                    </div>
                    <v-chart class="chart" :option="dashOpt1" />
                </el-card>
            </el-col>
            <el-col :span="6">
                <el-card shadow="hover">
                    <div class="card-header">
                        <p class="card-header-title">接触网跳闸故障种类分布</p>
                        <p class="card-header-desc">最近三年接触网跳闸故障的种类情况</p>
                    </div>
                    <v-chart class="chart" :option="dashOpt2" />
                </el-card>
            </el-col>
        </el-row>
        <el-row :gutter="20">
            <el-col :span="7">
                <el-card shadow="hover" :body-style="{ height: '400px' }">
                    <div class="card-header">
                        <p class="card-header-title">系统分析流程</p>
                        <p class="card-header-desc">高铁大数据智能分析流程</p>
                    </div>
                    <el-timeline>
                        <el-timeline-item v-for="(activity, index) in activities" :key="index" :color="activity.color">
                            <div class="timeline-item">
                                <div>
                                    <p>{{ activity.content }}</p>
                                    <p class="timeline-desc">{{ activity.description }}</p>
                                </div>
                                <div class="timeline-time">{{ activity.timestamp }}</div>
                            </div>
                        </el-timeline-item>
                    </el-timeline>
                </el-card>
            </el-col>
            <el-col :span="10">
                <el-card shadow="hover" :body-style="{ height: '400px' }">
                    <div class="card-header">
                        <p class="card-header-title">高铁站点统计</p>
                        <p class="card-header-desc">四川省内热门高铁站点客流量</p>
                    </div>
                    <v-chart class="map-chart" :option="mapOptions" />
                </el-card>
            </el-col>
            <el-col :span="7">
                <el-card shadow="hover" :body-style="{ height: '400px' }">
                    <div class="card-header">
                        <p class="card-header-title">排行榜</p>
                        <p class="card-header-desc">接触网跳闸故障Top5</p>
                    </div>
                    <div>
                        <div class="rank-item" v-for="(rank, index) in ranks">
                            <div class="rank-item-avatar">{{ index + 1 }}</div>
                            <div class="rank-item-content">
                                <div class="rank-item-top">
                                    <div class="rank-item-title">{{ rank.title }}</div>
                                    <div class="rank-item-desc">数量：{{ rank.value }}</div>
                                </div>
                                <el-progress
                                    :show-text="false"
                                    striped
                                    :stroke-width="10"
                                    :percentage="rank.percent"
                                    :color="rank.color"
                                />
                            </div>
                        </div>
                    </div>
                </el-card>
            </el-col>
        </el-row>
    </div>
</template>

<script setup lang="ts" name="dashboard">
import countup from '@/components/countup.vue';
import { use, registerMap } from 'echarts/core';
import { BarChart, LineChart, PieChart, MapChart } from 'echarts/charts';
import {
    GridComponent,
    TooltipComponent,
    LegendComponent,
    TitleComponent,
    VisualMapComponent,
} from 'echarts/components';
import { CanvasRenderer } from 'echarts/renderers';
import VChart from 'vue-echarts';
import { graphic } from 'echarts/core';
import sichuanMap from '@/utils/sichuan';
const dashOpt1 = {
    tooltip: {
        trigger: 'axis',
    },
    xAxis: {
        type: 'category',
        boundaryGap: false,
        data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
    },
    yAxis: {
        type: 'value',
    },
    legend: {
		data: ["真实值","预测值"]
	},
    grid: {
        top: '2%',
        left: '2%',
        right: '3%',
        bottom: '2%',
        containLabel: true,
    },
    color: ['#009688', '#f44336'],
    series: [
        {
            type: 'line',
            name: '入流量',
            smooth: true,
            data: [120, 132, 301, 134, 90, 230, 210],
        },
        {
            type: 'line',
            name: '出流量',
            smooth: true,
            data: [220, 122, 191, 234, 190, 130, 310],
        },
    ],
}
const dashOpt2 = {
    legend: {
        bottom: '1%',
        left: 'center',
    },
    color: ['#3f51b5', '#009688', '#f44336', '#00bcd4', '#1ABC9C'],
    series: [
        {
            type: 'pie',
            radius: ['40%', '70%'],
            avoidLabelOverlap: false,
            itemStyle: {
                borderRadius: 10,
                borderColor: '#fff',
                borderWidth: 2,
            },
            data: [
            { value: 2475, name: '过负荷' },
				{ value: 1320, name: '外部因素' },
				{ value: 289, name: '机车原因' },
				{ value: 408, name: '天气原因' },
				{ value: 101, name: '接触网线路' },
				{ value: 18, name: '变电设备' },
				{ value: 33, name: '其他原因' },
            ],
        },
    ],
}
const mapOptions = {
    tooltip: {
        trigger: 'item',
    },
    geo: {
        map: 'sichuan',
        roam: false,
        emphasis: {
            label: {
                show: false,
            },
        },
    },
    visualMap: {
        show: false,
        min: 0,
        max: 5,
        realtime: false,
        calculable: false,
        inRange: {
            color: ['#d2e0f5', '#3df31d', '#71A9FF'],
        },
    },
    series: [
        {
            geoIndex: 0,
            name: '地域分布',
            type: 'map',
            coordinateSystem: 'geo',
            map: 'sichuan',
            data: [
                { name: '成都市', value: 4 },
                { name: '绵阳市', value: 4 },
                { name: '德阳市', value: 7 },
                { name: '广安市', value: 6 },
                { name: '甘孜藏族自治州', value: 1 },
                { name: '阿坝藏族羌族自治州', value: 1 },
                { name: '凉山彝族自治州', value: 1 },
                { name: '广元市', value: 1 },
                { name: '巴中市', value: 1 },
                { name: '南充市', value: 6 },
                { name: '遂宁市', value: 1 },
                { name: '达州市', value: 9 },
                { name: '内江市', value: 1 },
                { name: '资阳市', value: 1 },
                { name: '自贡市', value: 1 },
                { name: '宜宾市', value: 1 },
                { name: '泸州市', value: 1 },
                { name: '攀枝花市', value: 1 },
                { name: '乐山市', value: 1 },
                { name: '眉山市', value: 1 },
                { name: '雅安市', value: 1 },
            ],
        },
    ],
};

use([
    CanvasRenderer,
    BarChart,
    GridComponent,
    LineChart,
    PieChart,
    TooltipComponent,
    LegendComponent,
    TitleComponent,
    VisualMapComponent,
    MapChart,
]);
registerMap('sichuan', sichuanMap);
const activities = [
    {
        content: '智能分析',
        description: '交通流量预测以及电气故障分类',
        timestamp: '动态分析',
        color: '#00bcd4',
    },
    {
        content: '模型推断',
        description: '使用部署好的模型对数据进行预测',
        timestamp: '实时推断',
        color: '#1ABC9C',
    },
    {
        content: '模型部署',
        description: '将训练好的模型稳定部署在服务器',
        timestamp: '稳定服务',
        color: '#3f51b5',
    },
    {
        content: '模型训练',
        description: '使用收集到的数据训练深度学习模型',
        timestamp: '动态学习',
        color: '#f44336',
    },
    {
        content: '数据收集',
        description: '针对客运和车辆两个场景进行收集',
        timestamp: '实时收集',
        color: '#009688',
    },
];

const ranks = [
    {
        title: '过负荷',
        value: 2475,
        percent: 53.2946,
        color: '#f25e43',
    },
    {
        title: '外部因素',
        value: 1320,
        percent: 28.4238,
        color: '#00bcd4',
    },
    {
        title: '天气原因',
        value: 408,
        percent: 8.7855,
        color: '#64d572',
    },
    {
        title: '机车原因',
        value: 289,
        percent: 6.2231,
        color: '#e9a745',
    },
    {
        title: '接触网线路',
        value: 101,
        percent: 2.1748,
        color: '#009688',
    },
];
</script>

<style>
.card-body {
    display: flex;
    align-items: center;
    height: 100px;
    padding: 0;
}
</style>
<style scoped>
.card-content {
    flex: 1;
    text-align: center;
    font-size: 14px;
    color: #999;
    padding: 0 20px;
}

.card-num {
    font-size: 30px;
}

.card-icon {
    font-size: 50px;
    width: 100px;
    height: 100px;
    text-align: center;
    line-height: 100px;
    color: #fff;
}

.bg1 {
    background: #2d8cf0;
}

.bg2 {
    background: #64d572;
}

.bg3 {
    background: #f25e43;
}

.bg4 {
    background: #e9a745;
}

.color1 {
    color: #2d8cf0;
}

.color2 {
    color: #64d572;
}

.color3 {
    color: #f25e43;
}

.color4 {
    color: #e9a745;
}

.chart {
    width: 100%;
    height: 400px;
}

.card-header {
    padding-left: 10px;
    margin-bottom: 20px;
}

.card-header-title {
    font-size: 18px;
    font-weight: bold;
    margin-bottom: 5px;
}

.card-header-desc {
    font-size: 14px;
    color: #999;
}

.timeline-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 16px;
    color: #000;
}

.timeline-time,
.timeline-desc {
    font-size: 12px;
    color: #787878;
}

.rank-item {
    display: flex;
    align-items: center;
    margin-bottom: 20px;
}

.rank-item-avatar {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background: #f2f2f2;
    text-align: center;
    line-height: 40px;
    margin-right: 10px;
}

.rank-item-content {
    flex: 1;
}

.rank-item-top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: #343434;
    margin-bottom: 10px;
}

.rank-item-desc {
    font-size: 14px;
    color: #999;
}
.map-chart {
    width: 100%;
    height: 350px;
}
</style>
