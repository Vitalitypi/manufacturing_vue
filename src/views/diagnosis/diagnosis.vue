<template>
	<div class="container">
		<el-card class="mgb20" shadow="hover">
			<template #header>
				<div class="header">
					<div class="content-title">故障实时诊断</div>
				</div>

			</template>
			<v-chart class="schart" :option="pieOptions" />
		</el-card>
	</div>
</template>

<script setup lang="ts" name="echarts">
import { use } from 'echarts/core';
import VChart from 'vue-echarts';
import { PieChart } from 'echarts/charts';
import { ref } from 'vue';
import axios from 'axios';
const pieOptions = ref({
	title: {
		text: '接触网跳闸故障实时诊断',
		left: 'center',
	},
	tooltip: {
		trigger: 'item',
	},
	legend: {
		orient: 'vertical',
		left: 'left',
	},
	series: [
		{
			name: '预测值',
			type: 'pie',
			radius: '50%',
			center: ['25%', '50%'],
			data: [
				{ value: 2475, name: '过负荷' },
				{ value: 1320, name: '外部因素' },
				{ value: 289, name: '机车原因' },
				{ value: 408, name: '天气原因' },
				{ value: 101, name: '接触网线路' },
				{ value: 18, name: '变电设备' },
				{ value: 33, name: '其他原因' },
			],
			emphasis: {
				itemStyle: {
					shadowBlur: 10,
					shadowOffsetX: 0,
					shadowColor: 'rgba(0, 0, 0, 0.5)',
				},
			},
		},
		{
			name: '真实值',
			type: 'pie',
			radius: '50%',
			center: ['75%', '50%'],
			data: [
				{ value: 2475, name: '过负荷' },
				{ value: 1320, name: '外部因素' },
				{ value: 289, name: '机车原因' },
				{ value: 408, name: '天气原因' },
				{ value: 101, name: '接触网线路' },
				{ value: 18, name: '变电设备' },
				{ value: 33, name: '其他原因' },
			],
			emphasis: {
				itemStyle: {
					shadowBlur: 10,
					shadowOffsetX: 0,
					shadowColor: 'rgba(0, 0, 0, 0.5)',
				},
			},
		},
	],
});
let idx = 0;
use([
	PieChart,
]);
const fetchData = async () => {
	try {
		let nums1 = [],nums2 = [];
		for (let i = 0; i < 7; i++) {
			nums1.push(0)
			nums2.push(0)
		}
		const response = await axios.get('http://127.0.0.1:5000/diagnosis?idx=' + idx);
		for (let i = 0; i < response.data.pred.length; i++) {
			const tp1 = response.data.pred[i];
			nums1[tp1]++
			const tp2 = response.data.true[i];
			nums2[tp2]++
		}
		for(let i = 0;i<7;i++){
			pieOptions.value.series[0].data[i].value = nums1[i]
			pieOptions.value.series[1].data[i].value = nums2[i]
		}
		idx += 500
	} catch (error) {
		console.error('请求失败:', error);
	}
};
fetchData()
setInterval(fetchData, 3000);
</script>

<style scoped>
.schart {
	width: 100%;
	height: 400px;
}

.header {
	display: flex;
	/* 使用flex布局 */
}

.content-title {
	width: 50%;
	font-weight: 400;
	font-size: 22px;
	color: #1f2f3d;
}

.el-select {
	width: 10%;
}
</style>
