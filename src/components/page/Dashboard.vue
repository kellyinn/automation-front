<template>
    <div>
        <el-row :gutter="20">
            <!-- 报警信息table -->
            <el-col :span="16">
                <el-card shadow="hover" class="mgb20" style="height:350px;">
                    <div slot="header" class="clearfix">
                        <span>报警信息:</span>
                    </div>
                    <el-table :data="tableData" border style="width: 100%">
                        <el-table-column prop="date" label="标题5" width="180"></el-table-column>
                        <el-table-column prop="name" label="批次" width="180"></el-table-column>
                        <el-table-column prop="address" label="原因"></el-table-column>
                        <el-table-column prop="option" label="操作"></el-table-column>
                    </el-table>
                </el-card>
            </el-col>
            <!-- 运行情况统计pie -->
            <el-col :span="8">
                <el-card shadow="hover" style="height:350px;">
                    <div slot="header" class="clearfix">
                        <span>运行情况统计</span>
                        <!-- <el-button style="float: right; padding: 3px 0" type="text">添加</el-button> -->
                    </div>                  
                    <schart class="wrapper" canvasId="canvas1" type="pie" :data="data1" :options="options1"></schart>                    
                </el-card>
            </el-col>
        </el-row>
        <el-row :gutter="20">
            <!-- 批量运行时间统计line -->
            <el-col :span="14">
                <el-card shadow="hover" style="height:380px;">
                    <div slot="header" class="clearfix">
                        <span>当日批量完成度百分比</span>                               
                    </div>  
                    <schart ref="line" class="schart" canvasId="line" :data="data2" type="line" :options="options2"></schart>
                </el-card>
            </el-col>
            <!-- 作业清单table -->
            <el-col :span="10">
                <el-card shadow="hover" style="height:380px;">
                    <div slot="header" class="clearfix">
                        <span>T + > 1 作业清单</span>
                    </div>   
                    <el-table :data="jobData" border style="width: 100%">
                        <el-table-column prop="date" label="表单"></el-table-column>
                        <el-table-column prop="name" label="批次"></el-table-column>
                    </el-table>
                </el-card>
            </el-col>
        </el-row>
    </div>
</template>

<script>
    import Schart from 'vue-schart';
    import bus from '../common/bus';
    export default {
        name: 'dashboard',
        data() {
            return {
                name: localStorage.getItem('ms_username'),
                tableData: [{
                    date: '2016-05-02',
                    name: '20190815',
                    address: '数据文件未找到',
                    option: '通知原系统'
                }, {
                    date: '2016-05-04',
                    name: '20190821',
                    address: '数据乱码',
                    option: '查看详情'
                }, {
                    date: '2016-05-01',
                    name: '20190823',
                    address: 'impala超时',
                    option: '重跑'
                }, {
                    date: '2016-05-03',
                    name: '20190825',
                    address: '表结构变动',
                    option: '通知大数据'
                }],
                data1: [
				    {name: 'running', value: 20},
				    {name: 'done', value: 30},
				    {name: 'pending', value: 10},
                    {name: 'fail', value: 40},
                ],
                data2: [{
                        name: '2018/09/04',
                        value: 1083
                    },
                    {
                        name: '2018/09/05',
                        value: 941
                    },
                    {
                        name: '2018/09/06',
                        value: 1139
                    },
                    {
                        name: '2018/09/07',
                        value: 816
                    },
                    {
                        name: '2018/09/08',
                        value: 327
                    },
                    {
                        name: '2018/09/09',
                        value: 228
                    },
                    {
                        name: '2018/09/10',
                        value: 1065
                    }
                ],
               
                options1: {				  
                    radius: 60, 
                },
                options2: {
                    //title: '批量运行时间统计',
                    fillColor: '#FC6FA1',
                    axisColor: '#008ACD',
                    contentColor: '#EEEEEE',
                    bgColor: '#FFFFFF',
                    bottomPadding: 30,
                    topPadding:1
                }
            }
        },
        components: {
            Schart
        },
        computed: {
            role() {
                return this.name === 'admin' ? '超级管理员' : '普通用户';
            }
        },
        created(){
            this.handleListener();
            this.changeDate();
        },
        activated(){
            this.handleListener();
        },
        deactivated(){
            window.removeEventListener('resize', this.renderChart);
            bus.$off('collapse', this.handleBus);
        },
        methods: {
            changeDate(){
                const now = new Date().getTime();
                this.data.forEach((item, index) => {
                    const date = new Date(now - (6 - index) * 86400000);
                    item.name = `${date.getFullYear()}/${date.getMonth()+1}/${date.getDate()}`
                })
            },
            handleListener(){
                bus.$on('collapse', this.handleBus);
                // 调用renderChart方法对图表进行重新渲染
                window.addEventListener('resize', this.renderChart)
            },
            handleBus(msg){
                setTimeout(() => {
                    this.renderChart()
                }, 300);
            },
            renderChart(){
                this.$refs.pie.renderChart();
                this.$refs.line.renderChart();
            }
        }
    }

</script>


<style scoped>
    .el-row {
        margin-bottom: 20px;
    }

    .grid-content {
        display: flex;
        align-items: center;
        height: 100px;
    }

    .grid-cont-right {
        flex: 1;
        text-align: center;
        font-size: 14px;
        color: #999;
    }

    .grid-num {
        font-size: 30px;
        font-weight: bold;
    }

    .grid-con-icon {
        font-size: 50px;
        width: 100px;
        height: 100px;
        text-align: center;
        line-height: 100px;
        color: #fff;
    }

    .grid-con-1 .grid-con-icon {
        background: rgb(45, 140, 240);
    }

    .grid-con-1 .grid-num {
        color: rgb(45, 140, 240);
    }

    .grid-con-2 .grid-con-icon {
        background: rgb(100, 213, 114);
    }

    .grid-con-2 .grid-num {
        color: rgb(45, 140, 240);
    }

    .grid-con-3 .grid-con-icon {
        background: rgb(242, 94, 67);
    }

    .grid-con-3 .grid-num {
        color: rgb(242, 94, 67);
    }

    .user-info {
        display: flex;
        align-items: center;
        padding-bottom: 20px;
        border-bottom: 2px solid #ccc;
        margin-bottom: 20px;
    }

    .user-avator {
        width: 120px;
        height: 120px;
        border-radius: 50%;
    }

    .user-info-cont {
        padding-left: 50px;
        flex: 1;
        font-size: 14px;
        color: #999;
    }

    .user-info-cont div:first-child {
        font-size: 30px;
        color: #222;
    }

    .user-info-list {
        font-size: 14px;
        color: #999;
        line-height: 25px;
    }

    .user-info-list span {
        margin-left: 70px;
    }

    .mgb20 {
        margin-bottom: 20px;
    }

    .todo-item {
        font-size: 14px;
    }

    .todo-item-del {
        text-decoration: line-through;
        color: #999;
    }

    .schart {
        width: 100%;
        height: 300px;
    }

</style>
