<template>
	<section>
		<!-- 工具条 -->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true" :model="filters">
				<el-form-item>
					<div class="block">
                      <span class="demonstration">时间</span>
                      <el-date-picker
                        v-model="time_value"
                        type="daterange"
                        range-separator=" - "
                        placeholder="选择日期范围">
                      </el-date-picker>
                    </div>
				</el-form-item>
					
				<el-form-item>
					<el-button type="primary" v-on:click="getUser">查询</el-button>
				</el-form-item>
			</el-form>
		</el-col>
	</section>
</template>
<script>
import { allget } from '../api/api';
import echarts from 'echarts';
	export default {
		data() {
			return {}
		}
	};

</script>

<style lang="scss" scoped>
	
</style>


