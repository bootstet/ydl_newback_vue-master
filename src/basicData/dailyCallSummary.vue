<template>
	<section>
		<!--工具条-->
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

				<el-form-item style="float:right">
					<el-button type="primary" v-on:click="getUser">查询</el-button>
					<el-button type="primary" v-on:click="handleDownload">导出</el-button>
				</el-form-item>
			</el-form>
		</el-col>
		<!-- 图形 -->
		<el-col :span="24" style="overflow-x:hidden">
			<div id="chartLine" style="width:100%; height:400px;"></div>
		</el-col>
		<!--列表-->
		<template>
			<el-table :data="pageTw" border fit highlight-current-row v-loading="listLoading" @selection-change="selsChange" style="width: 100%;" max-height="380" >
				<el-table-column type="index" width="70" >
				</el-table-column>
				<el-table-column prop="time" label="日期" width="120" sortable >
				</el-table-column>
				<el-table-column prop="day_best" label="日活跃人数" width="100" sortable>
				</el-table-column>
				<el-table-column prop="a" label="随机通话人数" width="100" sortable>
				</el-table-column>
				<el-table-column prop="b" label="大于1分钟随机人数" width="100" sortable>
				</el-table-column>
				<el-table-column prop="c" label="好友通话人数" min-width="100" sortable>
				</el-table-column>
				<el-table-column prop="d" label="大于1分钟好友人数" min-width="100" sortable>
				</el-table-column>
				<el-table-column prop="e" label="网红通话人数" min-width="100" sortable>
				</el-table-column>
				<el-table-column prop="f" label="大于1分钟网红人数" min-width="100" sortable>
				</el-table-column>
				<el-table-column prop="g" label="抢聊通话人数" min-width="100" sortable>
				</el-table-column>
				<el-table-column prop="h" label="大于1分钟抢聊人数" min-width="100" sortable>
				</el-table-column>
				<el-table-column prop="i" label="总通话人数" min-width="100" sortable>
				</el-table-column>
				<el-table-column prop="j" label="大于1分钟总人数" min-width="100" sortable>
				</el-table-column>
				<el-table-column prop="k" label="总通话比" min-width="100" sortable>
				</el-table-column>
				<el-table-column prop="l" label="大于一分钟通话比" min-width="100" sortable>
				</el-table-column>
			</el-table>
			<!--工具条-->
			<el-col :span="24" class="toolbar">
				<el-pagination layout="total,prev, pager, next,jumper" @current-change="handleCurrentChange" :page-size="20" :total="totalpage" style="float:right;">
				</el-pagination>
			</el-col>
		</template>
	</section>
</template>
<script>
	import { allget } from '../api/api';
	import echarts from 'echarts';
		export default{
			data(){
				return {
					star:'0',
					end:'20',
                    time_value:new Date(),
				}
			},
			computed:{
				pegeTw:function(){
					return this.users.slice(this.star,this.end)
				}
			},
			methods:{
				// 页面的页数
                handleCurrentChange(val) {
                    // console.log(val);
                    this.page = val;
                    this.star = this.page*20-20;
                    this.end = this.star+20;
                },
                // 时间格式化
                YMDdata:function(data){
                    let date = new Date(data);
                    let yy = date.getFullYear();
                    let mm = (date.getMonth() + 1).toString();
                    let dd = (date.getDate()).toString();
                    // let time = date.getFullYear() + '-' + (d.getMonth() + 1) + '-' + d.getDate();
                    mm<10?mm="0"+mm:mm;
                    dd<10?dd="0"+dd:dd;
                    return yy+'-'+mm+'-'+dd
                },
                //获取用户列表
                getUser() {
                    let _this = this ;
                    this.listLoading = true;
                    _this.alltime=[];
                    _this.obj=[];
                    let url = '/Base/getDayActive';
                    let data ={
                        date_s:this.YMDdata(this.time_value[0]),
                        date_e:this.YMDdata(this.time_value[1]),
                    }
                    allget(data, url).then(data => {
                        console.log(data);
                    // console.log('获取用户信息');
                    _this.totalpage = data.data.data.length;
                    _this.users = data.data.data;
                    let ajaxData = data.data.data;
                    ajaxData.forEach(function(val,index){
                        _this.alltime.unshift(ajaxData[index].time);
                        _this.obj.push({
                            name: ajaxData[index].time,
                            type: 'line',
                            smooth:true,
                            data: [ajaxData[index].a,ajaxData[index].b,ajaxData[index].c,ajaxData[index].d,ajaxData[index].e,ajaxData[index].f,ajaxData[index].g,ajaxData[index].h,ajaxData[index].i,ajaxData[index].j,ajaxData[index].k,ajaxData[index].l,ajaxData[index].m,ajaxData[index].n,ajaxData[index].o,ajaxData[index].p,ajaxData[index].q,ajaxData[index].r,ajaxData[index].s,ajaxData[index].t,ajaxData[index].u,ajaxData[index].v,ajaxData[index].w,ajaxData[index].x]
                        })
                    });

                    this.canvas();
                }).catch(function(err){
                        console.log(err);
                    });

                    this.listLoading = false;
                },
			}
		}
</script>

<style scoped>

</style>


