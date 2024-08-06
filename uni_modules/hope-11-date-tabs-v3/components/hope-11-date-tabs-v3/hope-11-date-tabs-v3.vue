<template>
	<view class="date-tabs-container" :style="{backgroundColor: bgColor ? bgColor : ''}">
		<view class="tabs-wrapper">
			<scroll-view scroll-x :show-scrollbar="false" :scroll-left="scrollLeft" scroll-with-animation class="scroll-view">
				<view class="date-wrapper">
					<view v-for="(item, index) in list" :key="index" class="date-item" @click="onItemClick(index)">
						<view class="week" :style="{color: index === current ? color : ''}">{{ item.w }}</view>
						<view class="date" :class="{current : index === current}" :style="{
								backgroundColor: index === current && !plain ? color : '',
								borderColor: index === current ? color : '',
								color: index === current && plain ? color : '',
								borderRadius: index === current && circle ? '50%' : ''
							}">{{ item.d }}</view>
					</view>
				</view>
			</scroll-view>
		</view>
		<view class="calendar-button" @click="onOpenCalendar">
			<Icons v-if="plain" type="calendar" size="36" :color="color"></Icons>
			<Icons v-else type="calendar-filled" size="36" :color="color"></Icons>
		</view>

		<Calendar ref="calendarRef" :insert="false" :date="pickerValue" :startDate="calendarStartDate"
			:endDate="calendarEndDate" :color="color" :plain="plain" :circle="circle" @confirm="onCalendarConfirm"></Calendar>
	</view>
</template>
<script setup name="DateTabs">
	import {
		ref,
		computed,
		watch,
		getCurrentInstance,
		nextTick
	} from 'vue'
	import {
		onLoad,
	} from '@dcloudio/uni-app'
	import dayjs from './dayjs/esm/index';
	import Calendar from './uni-calendar/uni-calendar.vue'
	import Icons from './uni-icons/uni-icons.vue'

	const emit = defineEmits(['update:modelValue', 'change'])

	const props = defineProps({
    modelValue: {
      type: String,
      default: '',
		},
		startDate: {
			type: String,
			default: ''
		},
		endDate: {
			type: String,
			default: ''
		},
		color: {
			type: String,
			default: '#007aff'
		},
		bgColor: {
			type: String,
			default: 'white'
		},
		plain: {
			type: Boolean,
			default: false
		},
		circle: {
			type: Boolean,
			default: false,
		}
	})

	const pickerValue = ref('')
	const list = ref([])
	const current = ref(0)
	const scrollLeft = ref(0)
	const dateItemWidth = ref(0)
	const weekdays = ['日', '一', '二', '三', '四', '五', '六']

	const calendarStartDate = computed(() => {
		return props.startDate || dayjs().format('YYYY-MM-DD')
	})

	const calendarEndDate = computed(() => {
		return props.endDate || dayjs(calendarStartDate.value).add(27, 'd').format('YYYY-MM-DD')
	})

	const initList = () => {
		const length = dayjs(calendarEndDate.value).diff(dayjs(calendarStartDate.value), 'day')
		for (let i = 0; i <= length; i++) {
			const date = dayjs(calendarStartDate.value).add(i, 'd')
			list.value.push({
				date: date.toDate(),
				dd: date.format('YYYY-MM-DD'),
				d: date.format('D'),
				w: date.isSame(dayjs(), 'day') ? '今' : (date.format('D') === '1' ? date.format('M月') : weekdays[date
					.day()])
			})
		}

		const fulldate = props.modelValue || dayjs().format('YYYY-MM-DD')
		for (let i = 0; i < list.value.length; i++) {
			if (list.value[i].dd === fulldate) {
				current.value = i
				scrollLeft.value = dateItemWidth.value * i + Math.random()
				break
			}
		}
		
		emit('update:modelValue', list.value[current.value].dd)
	}

	const onItemClick = (index) => {
		current.value = index
		emit('update:modelValue', list.value[current.value].dd)
		emit('change', list.value[current.value])
	}

	const calendarRef = ref()
	const onOpenCalendar = () => {
		pickerValue.value = list.value[current.value].dd
		calendarRef.value.open()
	}

	const onCalendarConfirm = (e) => {
		for (let i = 0; i < list.value.length; i++) {
			if (list.value[i].dd === e.fulldate) {
				onItemClick(i)
				scrollLeft.value = dateItemWidth.value * i + Math.random()
				break
			}
		}
	}

	watch(() => dateItemWidth.value, () => {
		for (let i = 0; i < list.value.length; i++) {
			if (list.value[i].dd === props.modelValue) {
				scrollLeft.value = dateItemWidth.value * i + Math.random()
				break
			}
		}
	})

	const instance = getCurrentInstance()
	onLoad(() => {
		initList()

		nextTick(() => {
			const query = uni.createSelectorQuery().in(instance)
			query.select('.date-item').boundingClientRect(res => {
				dateItemWidth.value = res.width
			}).exec()
		})
	})
</script>
<style lang="scss" scoped>
	.date-tabs-container {
		width: 100vw;
		height: 120rpx;
		box-shadow: 0 10rpx 10rpx #f8f8f8;
		display: flex;
		justify-content: space-between;
		align-items: center;

		.tabs-wrapper {
			width: calc(100% - 120rpx);

			.scroll-view {
				height: 100%;
				padding-bottom: 4px;

				/* #ifdef H5 */
				// 滚动条的宽度
				::-webkit-scrollbar {
					height: 6px !important;
				}

				::-webkit-scrollbar-track-piece {
					background-color: rgba(144, 147, 153, 0);
				}

				// 滚动条的设置
				::-webkit-scrollbar-thumb {
					background-color: rgba(144, 147, 153, 0.3);
					background-clip: padding-box;
					min-height: 28px;
					border-radius: 3px;
					transition: 0.3s background-color;
				}

				::-webkit-scrollbar-thumb:hover {
					background-color: rgba(144, 147, 153, 0.5);
				}
				/* #endif */
				
				.date-wrapper {
					display: flex;

					.date-item {
						height: 120rpx;
						display: flex;
						flex-direction: column;
						justify-content: center;
						align-items: center;

						.week, .date {
							width: 60rpx;
							margin: 5rpx 20rpx;
							display: flex;
							justify-content: center;
							align-items: center;
						}

						.week {
							font-size: 24rpx;
							color: grey;
						}

						.date {
							height: 60rpx;
							white-space: nowrap;
						}

						.current {
							box-sizing: border-box;
							border-width: 2px;
							border-style: solid;
							border-radius: 4px;
							color: white;
							font-weight: bold;
						}
					}
				}
			}
		}

		.calendar-button {
			width: 120rpx;
			height: 100%;
			box-shadow: -10rpx 0 10rpx #f8f8f8;
			display: flex;
			justify-content: center;
			align-items: center;
		}
	}
</style>