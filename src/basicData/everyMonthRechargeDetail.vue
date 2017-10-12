<template>
	<section>
		<!--charts 饼图-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true" :model="filters">
				<el-form-item>
					<div class="block">
						<span class="demonstration">时间</span>
						<el-date-picker
								v-model="start_time_one"
								type="month"
								placeholder="选择日期范围">
						</el-date-picker>
					</div>
				</el-form-item>

				<el-form-item style="float:right">
					<el-button type="primary" v-on:click="getGraphData">查询</el-button>
				</el-form-item>
			</el-form>
		</el-col>
		<!--echarts绘图-->
		<div id="cavs">
			<div class="hide" id="texte">
			</div>
			<div id="container" style="min-width: 400px; height: 400px; max-width: 600px; float: left;"></div>
			<div id="containers" style="min-width: 400px; height: 400px; max-width: 600px; float: right;"></div>
		</div>


		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true" :model="filters">
				<el-form-item>
					<div class="block">
						<span class="demonstration">时间</span>
						<el-date-picker
								v-model="start_time"
								type="month"
								placeholder="选择日期范围">
						</el-date-picker>
						<el-date-picker
								v-model="end_time"
								type="month"
								min-time="start_time"
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
		<!--列表-->
		<template>
			<el-table :data="pageTw" border fit highlight-current-row v-loading="listLoading" @selection-change="selsChange" style="width: 100%;" max-height="380" >
				<el-table-column type="index" width="70" >
				</el-table-column>
				<el-table-column prop="date" label="日期" width="120" sortable >
				</el-table-column>
				<el-table-column prop="all_times" label="总次数" width="100" sortable>
				</el-table-column>
				<el-table-column prop="all_people" label="总人数" width="100" sortable>
				</el-table-column>
				<el-table-column prop="total_fee" label="总金额" width="100" sortable>
				</el-table-column>
				<el-table-column prop="onetimes" label="1元" min-width="80" sortable>
				</el-table-column>
                <el-table-column prop="sixtimes" label="6元" min-width="80" sortable>
				</el-table-column>
                <el-table-column prop="tentimes" label="10元" min-width="80" sortable>
				</el-table-column>
                <el-table-column prop="twentytimes" label="20元" min-width="80" sortable>
				</el-table-column>
                <el-table-column prop="fiftytimes" label="50元" min-width="80" sortable>
				</el-table-column>
                <el-table-column prop="hundredtimes" label="100元" min-width="80" sortable>
				</el-table-column>
                <el-table-column prop="thousandtimes" label="1000元" min-width="80" sortable>
				</el-table-column>
                <el-table-column prop="mantimes" label="男" min-width="80" sortable>
				</el-table-column>
                <el-table-column prop="womantimes" label="女" min-width="80" sortable>
				</el-table-column>
                <el-table-column prop="zhifubaotimes" label="支付宝" min-width="80" sortable>
				</el-table-column>
                <el-table-column prop="weixintimes" label="微信" min-width="80" sortable>
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
<script type="text/ecmascript-6">
import { allget } from '../api/api';
import echarts from 'echarts';
	export default {
		data() {
			return {
				filters: {
					name: ''
				},
				star:"0",
				end:'20',
			    users:[],
				chartLine: null,
				totalpage:null,
				page: 2,
				listLoading: false,
//				time_value:[new Date()-3*24*60*60*1000,new Date()],
				start_time:new Date()-6*30*24*60*60*1000,
				start_time_one:new Date(),
				end_time:new Date(),
                obj:[],
				sels: [],//列表选中列
				alltime:[],
				dayactive:[],
				newdevice:[],
				newregister:[],
				ajaxrate:[],
				ajaxact:[],
				//	图形渲染参数
				graphData:[],// 图形数据
				onetimes:"1213",
				sixtimes:"",
				tentimes:"",
				twentytimes:"",
				fiftytimes:"",
				hundredtimes:"",
				thousandtimes:"",
				aaa:[12,45,23,23,56]
			}
		},
		computed:{
			pageTw:function(){
				return this.users.slice(this.star,this.end)
			}
		},
		methods: {
			//性别显示转换
			formatSex: function (row, column) {
				return row.sex == 1 ? '男' : row.sex == 0 ? '女' : '未知';
			},
            //页面的页数
			handleCurrentChange(val) {
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
                return yy+'-'+mm
            },
			//获取用户列表
			getUser() {
                let _this = this ;
                this.listLoading = true;
				_this.alltime=[];
				_this.obj=[];
                let url = 'Money/getChatGoldChargeByMonth';
                let data ={
//                    date_s:this.YMDdata(this.time_value[0]),
//                    date_e:this.YMDdata(this.time_value[1]),
					month:this.YMDdata(this.start_time),
					month_end:this.YMDdata(this.end_time),
                }
                allget(data, url).then(data => {
//                    console.log(data);
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

//					this.canvas();
                }).catch(function(err){
					console.log(err);
				});
                this.listLoading = false;
			},
//			获取图形列表数据
			getGraphData() {
				let _this = this;
				let url = 'Money/getChatGoldChargeByMonth'
				let data = {
					month:this.YMDdata(this.start_time_one)
				}
				allget(data,url).then(data => {
					_this.graphData = [];
					_this.graphData.push(data.data.data[0].onetimes,data.data.data[0].sixtimes,data.data.data[0].tentimes,data.data.data[0].twentytimes,
					data.data.data[0].fiftytimes,data.data.data[0].hundredtimes,data.data.data[0].thousandtimes);
					_this.onetimes = data.data.data[0].onetimes;
					_this.sixtimes = data.data.data[0].sixtimes;
					_this.tentimes = data.data.data[0].tentimes;
					_this.twentytimes = data.data.data[0].twentytimes;
					_this.fiftytimes = data.data.data[0].fiftytimes;
					_this.hundredtimes = data.data.data[0].hundredtimes;
					_this.thousandtimes = data.data.data[0].thousandtimes;

					_this.aaa = [12,45,78,99];
					_this.aaa.push(23)
					console.log("这里运行了没有",_this.aaa)
//					_this.aaa = [
//						{value:_this.onetimes,name:"1元"},
//						{value:_this.sixtimes,name:"6元"},
//						{value:_this.tentimes,name:"10元"},
//						{value:_this.twentytimes,name:"20元"},
//						{value:_this.fiftytimes,name:"50元"},
//						{value:_this.hundredtimes,name:"100元"},
//						{value:_this.thousandtimes,name:"1000元"},
//					]
				}).catch(function(err){
					console.log(err);
				})
				this.aaa = [2,2,2,2,2];
//				this.chartPie.setOption(option);
				console.log("打印aaa的值",this.aaa)
			},
			// 第二种导出方式
			handleDownload() {
	          	require.ensure([], () => {
  	            const { export_json_to_excel } = require('../vendor/Export2Excel');
  	            const tHeader = ['日期','总人数','日活跃','新增设备数','新增注册数','转化率','活跃度','平均使用时长','老活跃用户','老用户占比','男女占比'];
  	            const filterVal = ['time','people','active','device','register','rate','act','ACCU','old_active','old_rate','man_woman_rate'];
  	            const data = this.formatJson(filterVal, this.users);
  	            export_json_to_excel(tHeader, data, '总渠道数据');
  	          })
	        },
			// 导出的一部分东西
			formatJson(filterVal, jsonData) {
	          	return jsonData.map(v => filterVal.map(j => {
  	            if (j === 'timestamp') {
  	              return parseTime(v[j])
  	            } else {
  	              return v[j]
  	            }
  	          }))
		    },
			// 时间
			time:function(row, column){
				let dd = parseInt(row.ACCU / 60 / 60 / 24);
				let hh = parseInt(row.ACCU / 60 / 60 % 24);
				let mi = parseInt(row.ACCU / 60 % 60);
				let ss = parseInt(row.ACCU % 60);
				return dd + '天' + hh + '时' + mi + '分' + ss + '秒';
			},
			// 过滤器
			rate:function(row, column){
				let rate = '1'+':'+row.man_woman_rate
				return rate
			},
			selsChange: function (sels) {
				this.sels = sels;
			},
			// 图表绘制 已写死可以不死
			canvas:function(){
				this.chartLine = echarts.init(document.getElementById('chartLine'));
				this.chartLine.setOption({
	                title: {
	                    text: '总渠道数据管理'
	                },
	                tooltip: {
	                    trigger: 'axis'
	                },
	                legend: {
	                    data: this.alltime
	                },
					toolbox: {
				        show : true,
				        feature : {
				            mark : {show: false},
				            dataView : {show: false, readOnly: false},
				            magicType : {show: false, type: ['line', 'bar']},
				            restore : {show: false},
				            saveAsImage : {show: false}
				        }
				    },
	                containLabel: true,
	                xAxis: {
	                    type: 'category',
	                    data: ['0','1','3','4','5','6','7','8','9','10','11','12','13','14','15','16','17','18','19','20','21','22','23',24]
	                },
	                yAxis: [
	                     {
				            type : 'value',
				            name : '量',
				            axisLabel : {
				                formatter: '{value} .'
				            }
				        }
	                ],
	                series: this.obj
	            });
			}
		},
		mounted() {
            this.$nextTick(function(){
				this.getUser();
				this.getGraphData();
            })
//			初始化echarts实例
			this.chartPie = echarts.init(document.getElementById('container'));
			this.chartPie.setOption({
				title: {
					text: 'Pie Chart',
					subtext: '纯属虚构',
					x: 'center'
				},
				tooltip: {
					trigger: 'item',
					formatter: "{a} <br/>{b} : {c} ({d}%)"
				},
				legend: {
					orient: 'horizontal',
					bottom: 'bottom',
					data: ['1元', '6元','10元','20元','50元','100元','1000元']
				},
				series: [
					{
						name: '访问来源',
						type: 'pie',
						radius: '55%',
						center: ['50%', '60%'],
//						data: [{value:122,name:"1元"},
//							{value:222,name:"6元"}
//						],
						data:this.aaa,
						itemStyle: {
							emphasis: {
								shadowBlur: 10,
								shadowOffsetX: 0,
								shadowColor: 'rgba(0, 0, 0, 0.5)'
							}
						}
					}
				]
			});
		}
	};

</script>

<style scoped>

</style>
