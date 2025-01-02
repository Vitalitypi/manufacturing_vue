<template>
	<div class="container">
		<el-card class="mgb20" shadow="hover">
			<template #header>
				<div class="header">
					<el-select class="el-select" v-model="region" placeholder="请选择">
						<el-option v-for="option in array" :key="option.key" :label="option.label"
							:value="option.value">
						</el-option>
					</el-select>
					<div class="content-title">流量实时预测</div>
				</div>

			</template>
			<schart class="schart" canvasId="line" :options="options"></schart>
		</el-card>
	</div>
</template>

<script setup lang="ts" name="schart">
import Schart from 'vue-schart';
import { ref,watch } from 'vue';
const region = ref('各点平均'); // 用于v-model绑定的选中值
const array = ref([ // 选项数组，每个元素是一个对象
	{ value: '小明', label: '小明', key: '1' },
	{ value: '小红', label: '小红', key: '2' },
	{ value: '小白', label: '小白', key: '3' }
]);
const options = ref({
	type: 'line',
	title: {
		text: region.value+'未来流量实时预测折线图'
	},
	colorList: ["#3f51b5", "#009688", "#f44336", "#00bcd4", "#1ABC9C"],
	labels: ['6月', '7月', '8月', '9月', '10月'],
	datasets: [
		{
			label: '家电',
			data: [234, 278, 270, 190, 230]
		}
	]
});
// 监听selectedRegion的变化，并更新图表标题
watch(region, (newValue) => {
  if (newValue) {
    options.value.title.text = `${newValue}未来流量实时预测折线图`;
  }
});
//不断请求新的数据

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
