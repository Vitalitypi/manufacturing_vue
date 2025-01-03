<template>
	<div class="container">
		<el-card class="mgb20" shadow="hover">
			<template #header>
				<div class="header">
					<el-select class="el-select" v-model="region" placeholder="请选择">
						<el-option v-for="option in array" :key="option.key" :label="option.label" :value="option.key">
						</el-option>
					</el-select>
					<div class="content-title">客运流量实时预测</div>
				</div>

			</template>
			<v-chart class="schart" :option="options" />
		</el-card>
	</div>
</template>

<script setup lang="ts" name="echarts">
import { ElLoading } from 'element-plus'
import { use } from 'echarts/core';
import VChart from 'vue-echarts';
import { LineChart } from 'echarts/charts';
import { onMounted, onUnmounted, ref, watch } from 'vue';
import io from 'socket.io-client';
const num = 170;
let items = []
for (let i = 0; i < num; i++) {
	items.push({
		label: '传感器点' + (i),
		value: '传感器点' + (i),
		key: i
	})
}
const array = ref(items);
const region = ref(items[0].key); // 用于v-model绑定的选中值
let idx = 0;
let cache_true = []
let cache_pred = []
const options = ref({
	tooltip: {
		trigger: 'axis',
	},
	legend: {
		data: ["真实值", "预测值"]
	},
	grid: {
		left: '3%',
		right: '4%',
		bottom: '3%',
		containLabel: true,
	},
	xAxis: {
		type: 'category',
		boundaryGap: false,
		data: [],
	},
	yAxis: {
		type: 'value',
	},
	color: ['#009688', '#f44336'],
	series: [
		{
			name: '真实值',
			type: 'line',
			data: [],
		},
		{
			name: '预测值',
			type: 'line',
			data: [],
		},
	],
});
use([
	LineChart,
]);
// 监听selectedRegion的变化，并更新图表标题
watch(region, (key) => {
	if (key) {
		options.value.series[0].data = cache_true[key]
		options.value.series[1].data = cache_pred[key]
		// options.value.title.text = `${items[key].value}未来流量实时预测折线图`;
	}
});
const socket = ref(null);
onMounted(()=>{
	let loadingInstrance = ElLoading.service({
      lock: true,
      text: "正在加载...",
      background: "rgba(0, 0, 0, 0.5)",
    });
	socket.value = io('http://127.0.0.1:5000');
	socket.value.emit('railway_server', "predict");
	socket.value.on('predict', (response) => {
		console.log("received!")
		loadingInstrance.close();
		let t = response.true
		for (let i = 0; i < num; i++) {
			for (let j = 0; j < 12; j++) {
				t[i][j] = Math.round(t[i][j])
				if (cache_true.length != 0) {
					cache_pred[i].push(response.pred[i][j])
					cache_true[i].push(t[i][j])
				}
			}
		}
		if (cache_true.length == 0) {
			cache_pred = response.pred
			cache_true = t
		}
		options.value.series[0].data = cache_true[region.value]
		options.value.series[1].data = cache_pred[region.value]
		for (let i = 0; i < 12; i++) {
			options.value.xAxis.data.push(response.index + i)
		}
		idx += 12
		// console.log('Connected to the server.',response);
	});
	
})
onUnmounted(() => {
	console.log("onUnmounted")
  if (socket.value) {
    socket.value.disconnect();
    console.log('WebSocket disconnected on unmount');
  }
});

</script>

<style scoped>
.schart {
	width: 100%;
	height: 700px;
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
