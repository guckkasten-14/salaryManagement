<template>
<div id="app">
    <h3>vue+ElementUI的简单消费清单系统，端口9099，采用localStorage本地存储</h3>
    <p>2020</p>
    <el-form :model="form" ref="form">
        <el-table :data="form.datas" highlight-current-row style="width: 100%">
            <el-table-column prop="date" label="日期">
                <template slot-scope="scope">
                    <template v-if="scope.row.action == 'view'">
                        {{scope.row.date}}
                    </template>
                    <template v-if="scope.row.action == 'edit'">
                        <el-form-item :prop="'datas.'+scope.$index + '.date'">
                            <el-input size="mini" v-model.trim="scope.row.date" style="width: 120px;"></el-input>
                        </el-form-item>
                    </template>
                </template>
            </el-table-column>

            <el-table-column prop="realSalary" label="工资到手">
                <template slot-scope="scope">
                    <template v-if="scope.row.action == 'view'">
                        {{scope.row.realSalary}}
                    </template>
                    <template v-else>
                        <el-form-item :prop="'datas.'+scope.$index + '.realSalary'">
                            <el-input size="mini" v-model.trim="scope.row.realSalary" style="width: 120px;"></el-input>
                        </el-form-item>
                    </template>
                </template>
            </el-table-column>

            <el-table-column prop="rent" label="房租">
                <template slot-scope="scope">
                    <template v-if="scope.row.action == 'view'">
                        {{scope.row.rent}}
                    </template>
                    <template v-else>
                        <el-form-item :prop="'datas.'+scope.$index + '.rent'">
                            <el-input size="mini" v-model.number="scope.row.rent" style="width: 60px;"></el-input>
                        </el-form-item>
                    </template>
                </template>
            </el-table-column>

            <el-table-column prop="utilities" label="水电费">
                <template slot-scope="scope">
                    <template v-if="scope.row.action == 'view'">
                        {{scope.row.utilities}} / 2
                    </template>
                    <template v-else>
                        <el-form-item :prop="'datas.'+scope.$index + '.utilities'">
                            <el-input size="mini" v-model.trim="scope.row.utilities" style="width: 120px;"></el-input>
                        </el-form-item>
                    </template>
                </template>
            </el-table-column>

            <el-table-column prop="shop" label="购物+其它">
                <template slot-scope="scope">
                    <template v-if="scope.row.action == 'view'">
                        {{scope.row.shop}}
                    </template>
                    <template v-else>
                        <el-form-item :prop="'datas.'+scope.$index + '.shop'">
                            <el-input size="mini" v-model.trim="scope.row.shop" style="width: 120px;"></el-input>
                        </el-form-item>
                    </template>
                </template>
            </el-table-column>

            <el-table-column prop="remark" label="备注">
                <template slot-scope="scope">
                    <template v-if="scope.row.action == 'view'">
                        {{scope.row.remark}}
                    </template>
                    <template v-else>
                        <el-form-item :prop="'datas.'+scope.$index + '.remark'">
                            <el-input size="mini" v-model.trim="scope.row.remark" style="width: 120px;"></el-input>
                        </el-form-item>
                    </template>
                </template>
            </el-table-column>

            <el-table-column prop="deposit" label="存款">
                <template slot-scope="scope">
                    <template v-if="scope.row.action == 'view'">
                        {{scope.row.deposit}}
                    </template>
                    <template v-else>
                        <el-form-item :prop="'datas.'+scope.$index + '.deposit'">
                            <el-input size="mini" v-model.trim="scope.row.deposit" style="width: 120px;"></el-input>
                        </el-form-item>
                    </template>
                </template>
            </el-table-column>

            <el-table-column prop="operation" label="操作">
                <template slot-scope="scope">
                    <template v-if="scope.row.action == 'view'">
                        <el-button size="mini" @click="click_edit(scope.row, scope.$index)">编辑</el-button>
                        <el-button size="mini" @click="click_delete(scope.row, scope.$index)">删除</el-button>
                    </template>
                    <template v-else-if="scope.row.action == 'add'">
                        <el-button size="mini" @click="click_add( scope.row, scope.$index)">新增</el-button>
                        <el-button size="mini" @click="click_reset(scope.row, scope.$index)">重置</el-button>
                    </template>
                    <template v-else>
                        <el-button size="mini" @click="click_save(scope.row, scope.$index)">保存</el-button>
                        <el-button size="mini" @click="click_cancle(scope.row, scope.$index)">取消</el-button>
                    </template>
                </template>
            </el-table-column>
        </el-table>
    </el-form>
    <div @click="calcTotal">存款数字总和为：{{form.total}}（最后计算时间为 {{form.caclDate}}）</div>
    <div>Notes:<el-input v-model='form.notes' @change="inputChange"></el-input>
    </div>
</div>
</template>

<script>

</script>
<script>
export default {
    data() {
        return {
            form: {
                datas: [],
                notes: "",
                total: 0,
                caclDate: ""
            },
            //表单验证规则
            rules: {
                realSalary: [{
                    type: 'string',
                    required: true,
                    trigger: 'blur',
                    message: '请输入工资到手数字',
                }],
                rent: [{
                    type: 'string',
                    required: true,
                    trigger: 'blur',
                    message: '请输入房租数字',
                }],
                utilities: [{
                    type: 'string',
                    required: true,
                    trigger: 'blur',
                    message: '请输入水电费数字',
                }],
                shop: [{
                    type: 'string',
                    required: true,
                    trigger: 'blur',
                    message: '请输入购物数字',
                }],
            }
        }
    },

    created() {
        //处理数据，为已有数据添加action:'view'
        this.form.datas.map(item => {
            this.$set(item, "action", "view")
            return item;
        });

        //再插入一条添加操作的数据
        // this.form.datas.unshift({
        //     date: undefined,
        //     name: undefined,
        //     age: undefined,
        //     action: "add"
        // });

    },
    mounted() {
        this.getData()
    },
    methods: {
        //对部分表单字段进行校验
        validateField(form, index) {
            let result = true;
            for (let item of this.$refs[form].fields) {
                if (item.prop.split(".")[1] == index) {
                    this.$refs[form].validateField(item.prop, (error) => {
                        if (error != "") {
                            result = false;
                        }
                    });
                }
                if (!result) break;
            }
            return result;
        },

        //对部分表单字段进行重置
        resetField(form, index) {
            this.$refs[form].fields.forEach(item => {
                if (item.prop.split(".")[1] == index) {
                    item.resetField();
                }
            })
        },

        //组件刚加载时从localStorage里获取数据
        getData() {
            // let tempp = {
            //     "formDatas": [{
            //         "realSalary": "",
            //         "rent": "",
            //         "utilities": "",
            //         "shop": "",
            //         "remark": "",
            //         "deposit": "",
            //         "action": "add"
            //     }, {
            //         "realSalary": "7637.91",
            //         "rent": 1480,
            //         "utilities": "265.24",
            //         "shop": "3525.29",
            //         "remark": "租房子押金、和买了些家具",
            //         "deposit": "2500",
            //         "action": "view",
            //         "date": "2020-05-15"
            //     }, {
            //         "realSalary": "9239.42",
            //         "rent": 1480,
            //         "utilities": "424.31",
            //         "shop": "2547.27",
            //         "remark": "花呗欠太多了",
            //         "deposit": "5000",
            //         "action": "view",
            //         "date": "2020-06-15"
            //     }, {
            //         "realSalary": "9314.05",
            //         "rent": 1480,
            //         "utilities": "639.89",
            //         "shop": "2514.10",
            //         "remark": "花呗欠太多了",
            //         "deposit": "5000",
            //         "action": "view",
            //         "date": "2020-07-15"
            //     }, {
            //         "realSalary": "11155.58",
            //         "rent": 1480,
            //         "utilities": "960.54",
            //         "shop": "6695.31",
            //         "remark": "买了电脑4299",
            //         "deposit": "2500",
            //         "action": "view",
            //         "date": "2020-08-15"
            //     }, {
            //         "realSalary": "11561.86",
            //         "rent": 1480,
            //         "utilities": "646.72",
            //         "shop": "2758.50",
            //         "remark": "",
            //         "deposit": "7000",
            //         "action": "view",
            //         "date": "2020-09-15"
            //     }],
            //     "notes": "",
            //     "total": 0,
            //     "caclDate": "",
            // }
            // localStorage.setItem(JSON.stringify("salaryManagement"), JSON.stringify(tempp))

            let temp = JSON.parse(localStorage.getItem(JSON.stringify("salaryManagement")))
            if (!temp) {
                let obj = [{
                    "realSalary": "",
                    "rent": "",
                    "utilities": "",
                    "shop": "",
                    "remark": "",
                    "deposit": "",
                    "action": "add"

                }]
                this.form.datas = obj
            } else {
                this.form.datas = temp.formDatas
            }
            this.form.notes = temp.notes
            this.form.total = temp.total
            this.form.caclDate = temp.caclDate
        },

        inputChange() {
            let temp = JSON.parse(localStorage.getItem(JSON.stringify("salaryManagement")))
            temp.notes = this.form.notes
            localStorage.setItem(JSON.stringify("salaryManagement"), JSON.stringify(temp))
        },

        calcTotal() {
            let date = new Date().getFullYear() + '-' + new Date().getMonth() + '-' + new Date().getDate() + ' ' + new Date().getHours() + ':' + new Date().getMinutes()
            this.form.caclDate = date
            let count = 0
            this.form.datas.forEach(item => {
                count += Number(item.deposit)
            })
            this.form.total = count
            let temp = JSON.parse(localStorage.getItem(JSON.stringify("salaryManagement")))
            temp.total = this.form.total
            temp.caclDate = this.form.caclDate
            localStorage.setItem(JSON.stringify("salaryManagement"), JSON.stringify(temp))
        },

        //新增操作
        click_add(item, index) {
            //问题：为什么这里打印item出来总是空
            // console.log("item", item)
            // if (!this.validateField('form', index)) return;
            //模拟新增一条数据
            let year = new Date().getFullYear()
            let month = new Date().getMonth() + 1
            let date = new Date().getDate()
            if (month < 10) {
                month = "0" + month
            }
            if (date < 10) {
                date = "0" + date
            }
            let time = year + '-' + month + '-' + date

            let newObj = JSON.parse(JSON.stringify(item));
            newObj.shop = (Number(newObj.realSalary) - Number(newObj.rent) - Number(newObj.utilities * 0.5) - Number(newObj.deposit)).toFixed(2)
            newObj.date = time;
            newObj.action = "view";
            this.form.datas.push(newObj);

            this.resetField('form', index);

            let temp = JSON.parse(localStorage.getItem(JSON.stringify("salaryManagement")))
            temp.formDatas = this.form.datas
            localStorage.setItem(JSON.stringify("salaryManagement"), JSON.stringify(temp))

        },

        //新增-重置操作
        click_reset(item, index) {
            this.resetField('form', index);
        },

        //编辑-保存操作
        click_save(item, index) {
            console.log("item", item)
            // if (!this.validateField('form', index)) return;
            item.action = "view";
            item.shop = (Number(item.realSalary) - Number(item.rent) - Number(item.utilities * 0.5) - Number(item.deposit)).toFixed(2)
            this.form.datas.splice(index, 1, item);

            let temp = JSON.parse(localStorage.getItem(JSON.stringify("salaryManagement")))
            temp.formDatas = this.form.datas
            localStorage.setItem(JSON.stringify("salaryManagement"), JSON.stringify(temp))

        },

        //编辑-取消操作
        click_cancle(item, index) {
            this.resetField('form', index);
            item.action = "view";
        },

        //编辑操作
        click_edit(item, index) {
            item.action = "edit";
        },

        //删除操作
        click_delete(item, index) {
            this.$confirm("确定删除该条日期为" + item.date + "的数据吗?", "提示", {
                    confirmButtonText: "确定",
                    cancelButtonText: "取消",
                    type: "warning"
                })
                .then(() => {
                    //模拟删除一条数据
                    this.form.datas.splice(index, 1);
                    let temp = JSON.parse(localStorage.getItem(JSON.stringify("salaryManagement")))
                    temp.formDatas = this.form.datas
                    localStorage.setItem(JSON.stringify("salaryManagement"), JSON.stringify(temp))
                })
                .catch(() => {});
        },

    },
}
</script>

<style>
.el-table .cell {
    overflow: visible;
}

.el-form-item {
    margin-bottom: 0;
}

.el-form-item__error {
    padding-top: 0;
    margin-top: -3px;
}
</style>
