<template>
	<div class="calendar">
		<div class="check">
			<div class="check-left">
				<div @click="backYear">
					<img src="../assets/arrow-double-left.png" alt="" />
				</div>
				<div @click="backMonth">
					<img src="../assets/arrow-left.png" alt="" />
				</div>
			</div>
			<div>{{ checkYear }}年{{ checkMonth + 1 }}月</div>
			<div class="check-right">
				<div @click="forwardMonth">
					<img src="../assets/arrow-right.png" alt="" />
				</div>
				<div @click="forwardYear">
					<img src="../assets/arrow-double-right.png" alt="" />
				</div>
			</div>
		</div>
		<div class="week">
			<div>日</div>
			<div>一</div>
			<div>二</div>
			<div>三</div>
			<div>四</div>
			<div>五</div>
			<div>六</div>
		</div>
		<div class="day">
			<div v-for="(item, index) in checkWeek" :key="index + 'week'"></div>
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
			</div>
		</div>
		<div class="btn">
			<div class="btn_current" @click="backCurrentDate">返回当前日期</div>
		</div>
	</div>
</template>

<script>
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
	},
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.calendar {
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
	margin-bottom: 15px;
}
.calendar .day div {
	padding-top: 30px;
	text-align: center;
	width: calc(100% / 7);
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
	width: 30px;
	height: 30px;
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
</style>
