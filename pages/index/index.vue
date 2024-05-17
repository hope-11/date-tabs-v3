<template>
	<view class="content">
		<view class="wrapper">
			<view class="title">基础使用</view>
			<DateTabs v-model="value1" @change='onDateTabsChange'></DateTabs>
			<view class="value">当前日期：{{ value1 }}</view>
		</view>
		<view class="wrapper">
			<view class="title">设置当前日期、开始日期和结束日期</view>
			<DateTabs  v-model="value2" startDate="2024-06-01" endDate="2024-06-30"></DateTabs>
			<view class="value">当前日期：{{ value2 }}</view>
		</view>
		<view class="wrapper">
			<view class="title">设置朴素和圆形样式</view>
			<DateTabs  v-model="value3" plain circle></DateTabs>
			<view class="value">当前日期：{{ value3 }}</view>
		</view>
		<view class="wrapper">
			<view class="title">设置主题色</view>
			<DateTabs  v-model="value4" color="red" bgColor="rgba(255, 0, 0, 0.1)"></DateTabs>
			<view class="value">当前日期：{{ value4 }}</view>
		</view>
	</view>
</template>

<script setup>
	import {
		ref
	} from 'vue'
	import DateTabs from '@/uni_modules/hope-11-date-tabs-v3/components/hope-11-date-tabs-v3/hope-11-date-tabs-v3.vue'

	const value1 = ref('')
	const value2 = ref('2024-06-06')
	const value3 = ref('')
	const value4 = ref('')

	const onDateTabsChange = (e) => {
		console.log(e)
	}
</script>

<style lang="scss" scoped>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;

		.wrapper {
			margin: 20px 0;

			.title {
				margin-bottom: 10px;
				font-weight: bold;
				display: flex;
				justify-content: center;
				align-items: center;
			}

			.value {
				margin-top: 10px;
				display: flex;
				justify-content: center;
				align-items: center;
			}
		}
	}
</style>