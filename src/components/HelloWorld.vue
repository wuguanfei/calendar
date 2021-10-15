<template>
	<div class="calendar">
		<!-- 切换年月区域 -->
		<div class="check">
			<div class="check-left">
				<div @click="backYear">
					<img src="../assets/arrow-double-left.png" alt="" />
				</div>
				<div @click="backMonth">
					<img src="../assets/arrow-left.png" alt="" />
				</div>
			</div>
			<div>
				<span @click="openYaer" class="open_yaer">{{ checkYear }}年</span>
				<span @click="openMonth" class="open_month"
					>{{ checkMonth + 1 }}月</span
				>
			</div>
			<div class="check-right">
				<div @click="forwardMonth">
					<img src="../assets/arrow-right.png" alt="" />
				</div>
				<div @click="forwardYear">
					<img src="../assets/arrow-double-right.png" alt="" />
				</div>
			</div>
		</div>
		<!-- 星期 -->
		<div class="week">
			<div>日</div>
			<div>一</div>
			<div>二</div>
			<div>三</div>
			<div>四</div>
			<div>五</div>
			<div>六</div>
		</div>
		<!-- 日 -->
		<div class="day">
			<div v-for="(item, index) in checkWeek" :key="index + 'space'"></div>
			<div
				v-for="(item, index) in checkDays"
				:key="index + 'day'"
				:class="{
					active:
						index == currentDate - 1 &&
						checkYear == currentYear &&
						currentMonth == checkMonth,
				}"
			>
				{{ item }}
				<span
					class="lunar"
					:class="{ lunar_month: getLunarDay(item).IDayCn === '初一' }"
					>{{
						getLunarDay(item).IDayCn === '初一'
							? getLunarDay(item).IMonthCn
							: getLunarDay(item).IDayCn
					}}</span
				>
			</div>
		</div>
		<!-- 快速切换按钮 -->
		<div class="btn">
			<div class="btn_current" @click="backCurrentDate">返回当前日期</div>
		</div>
		<!-- 蒙层 -->
		<div class="mantle" v-show="mantleShow" @click="closeMantle"></div>
		<!-- 年弹框 -->
		<div class="check_dialog" :class="{ check_dialog_no: !yearDialogShow }">
			<div class="year_btn">
				<div @click="delOrAddYears('del')">
					<img src="../assets/arrow-double-left.png" alt="" />
				</div>
				<div>{{ this.startYear }}年-{{ this.startYear + 9 }}年</div>
				<div @click="delOrAddYears('add')">
					<img src="../assets/arrow-double-right.png" alt="" />
				</div>
			</div>
			<div
				class="view_width"
				v-for="(item, index) in 10"
				:key="item + 'year'"
				@click="jumpYear(startYear + index)"
			>
				{{ startYear + index }}年
			</div>
		</div>
		<!-- 月弹框 -->
		<div class="check_dialog" :class="{ check_dialog_no: !monthDialogShow }">
			<div
				class="view_width"
				v-for="(item, index) in 12"
				:key="item + 'month'"
				@click="jumpMonth(item)"
			>
				{{ index }}月
			</div>
		</div>
	</div>
</template>

<script>
import { calendar } from '../utils/lunarCalendar';
export default {
	data() {
		return {
			checkYear: 0, // 当月年
			checkMonth: 0, // 当月月
			checkDays: 0, // 当月总天数
			checkWeek: 0, // 当月星期
			currentYear: 0, // 当前年
			currentMonth: 0, // 当前月
			currentDate: 0, // 当前日期
			mantleShow: false, // 蒙层状态
			yearDialogShow: false, // 年弹框
			monthDialogShow: false, // 年弹框
			startYear: 0, // 年弹框起始年份
		};
	},
	created() {
		this.checkYear = new Date().getFullYear();
		this.checkMonth = new Date().getMonth();
		this.checkDays = this.getMonthDay(this.checkYear, this.checkMonth);
		this.checkWeek = this.getMonthWeek(this.checkYear, this.checkMonth);
		this.currentDate = new Date().getDate();
		this.currentMonth = new Date().getMonth();
		this.currentYear = new Date().getFullYear();
	},
	mounted() {},
	methods: {
		// 获取当月总天数
		getMonthDay(year, month) {
			let days = new Date(year, month + 1, 0).getDate();
			return days;
		},
		// 获取当月第一天的星期
		getMonthWeek(year, month) {
			let week = new Date(year, month, 1).getDay();
			return week;
		},
		// 上一年
		backYear() {
			this.checkYear = this.checkYear - 1;
			this.checkDays = this.getMonthDay(this.checkYear, this.checkMonth);
			this.checkWeek = this.getMonthWeek(this.checkYear, this.checkMonth);
		},
		// 下一年
		forwardYear() {
			this.checkYear = this.checkYear + 1;
			this.checkDays = this.getMonthDay(this.checkYear, this.checkMonth);
			this.checkWeek = this.getMonthWeek(this.checkYear, this.checkMonth);
		},
		// 上一月
		backMonth() {
			if (this.checkMonth == 0) {
				this.checkYear = this.checkYear - 1;
				this.checkMonth = 12;
			}
			this.checkMonth = this.checkMonth - 1;
			this.checkDays = this.getMonthDay(this.checkYear, this.checkMonth);
			this.checkWeek = this.getMonthWeek(this.checkYear, this.checkMonth);
		},
		// 下一月
		forwardMonth() {
			if (this.checkMonth == 11) {
				this.checkYear = this.checkYear + 1;
				this.checkMonth = -1;
			}
			this.checkMonth = this.checkMonth + 1;
			this.checkDays = this.getMonthDay(this.checkYear, this.checkMonth);
			this.checkWeek = this.getMonthWeek(this.checkYear, this.checkMonth);
		},
		// 返回当前日期
		backCurrentDate() {
			this.checkYear = new Date().getFullYear();
			this.checkMonth = new Date().getMonth();
			this.checkDays = this.getMonthDay(this.checkYear, this.checkMonth);
			this.checkWeek = this.getMonthWeek(this.checkYear, this.checkMonth);
		},
		// 关闭蒙层
		closeMantle() {
			this.mantleShow = false;
			this.yearDialogShow = false;
			this.monthDialogShow = false;
		},
		// 打开年份选择框
		openYaer() {
			this.mantleShow = true;
			this.yearDialogShow = true;
			this.startYear = Number(this.checkYear.toString().slice(0, 3) + '0');
		},
		// 打开月份选择框
		openMonth() {
			this.mantleShow = true;
			this.monthDialogShow = true;
		},
		// 跳转年
		jumpYear(year) {
			this.checkYear = year;
			this.checkDays = this.getMonthDay(this.checkYear, this.checkMonth);
			this.checkWeek = this.getMonthWeek(this.checkYear, this.checkMonth);
			this.mantleShow = false;
			this.yearDialogShow = false;
		},
		// 跳转月
		jumpMonth(month) {
			this.checkMonth = month;
			this.checkDays = this.getMonthDay(this.checkYear, this.checkMonth);
			this.checkWeek = this.getMonthWeek(this.checkYear, this.checkMonth);
			this.mantleShow = false;
			this.monthDialogShow = false;
		},
		// 弹框年增加(减少)
		delOrAddYears(type) {
			if (type === 'add') {
				this.startYear = this.startYear + 10;
			} else {
				this.startYear = this.startYear - 10;
			}
		},
		// 获取农历
		getLunarDay(day) {
			let lunarDate = calendar.solar2lunar(
				this.checkYear,
				this.checkMonth + 1,
				day
			);
			// console.log(lunarDate);
			return lunarDate;
		},
	},
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.calendar {
	position: relative;
	max-width: 500px;
	height: 100vh;
	color: #606266;
}
.check {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 15px 10px;
	font-size: 20px;
	font-weight: bolder;
}
.check .check-left div:nth-of-type(1) {
	margin-right: 10px;
}
.check .check-right div:nth-of-type(1) {
	margin-right: 10px;
}
.check div {
	display: flex;
}
.check img {
	width: 25px;
	height: 25px;
}
.open_yaer {
	margin-right: 5px;
}
.open_month {
}
.calendar .week {
	display: flex;
	justify-content: space-between;
	width: 100%;
}
.calendar .week div {
	text-align: center;
	width: calc(100% / 7);
}

.calendar .day {
	display: flex;
	flex-wrap: wrap;
	width: 100%;
	margin-bottom: 30px;
}
.calendar .day div {
	padding-top: 30px;
	text-align: center;
	width: calc(100% / 7);
	position: relative;
}
.calendar .day div .lunar {
	position: absolute;
	font-size: 12px;
	bottom: -16px;
	left: 52%;
	transform: translateX(-50%);
	color: #929bbf;
}
.calendar .day div .lunar_month {
	color: red !important;
}
.calendar .day .active {
	color: red;
	font-weight: bolder;
	position: relative;
}

.calendar .day .active::before {
	position: absolute;
	top: 50%;
	left: 52%;
	transform: translateX(-50%);
	content: '';
	width: 45px;
	height: 45px;
	border-radius: 50%;
	background-color: rgba(235, 107, 107, 0.5);
}
.calendar .btn {
	padding: 0 15px;
}

.calendar .btn .btn_current {
	height: 35px;
	line-height: 35px;
	padding: 0 10px;
	border-radius: 15px;
	color: #ffffff;
	background-color: rgb(206, 187, 187);
	display: inline-block;
}

.calendar .mantle {
	position: absolute;
	top: 0;
	width: 100%;
	height: 100%;
	background-color: rgba(0, 0, 0, 0.5);
}

.calendar .check_dialog {
	position: absolute;
	top: 45px;
	left: 50%;
	width: 300px;
	background-color: #ffffff;
	border: 1px solid #e4e7ed;
	box-shadow: 0 2px 12px 0 rgb(0 0 0 / 10%);
	background: #fff;
	border-radius: 8px;
	display: flex;
	flex-wrap: wrap;
	text-align: center;
	padding-bottom: 30px;
	transform: translateX(-50%) scale(1);
	transition: 0.5s;
}
.calendar .check_dialog_no {
	transform: translateX(-50%) scale(0) !important;
}
.calendar .check_dialog .view_width {
	width: calc(100% / 4);
	padding-top: 30px;
}
.calendar .check_dialog .year_btn {
	width: 100%;
	display: flex;
	justify-content: space-between;
	align-items: center;
	font-size: 20px;
	padding: 10px 10px 0 10px;
}
.calendar .check_dialog .year_btn img {
	width: 20px;
	height: 20px;
}
</style>
