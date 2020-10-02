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
                        {{scope.row.utilities}}
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
                this.form.datas = temp
            }
        },

        //新增操作
        click_add(item, index) {
            //问题：为什么这里打印item出来总是空
            // console.log("item", item)
            // if (!this.validateField('form', index)) return;
            //模拟新增一条数据

            let month = new Date().getMonth() + 1
            let date = new Date().getDate()
            if (date < 10) {
                date = "0" + date
            }
            let time = month + '-' + date

            let newObj = JSON.parse(JSON.stringify(item));
            newObj.deposit = Number(newObj.realSalary) - Number(newObj.rent) - Number(newObj.utilities) - Number(newObj.shop)
            newObj.date = time;
            newObj.action = "view";
            this.form.datas.push(newObj);

            this.resetField('form', index);

            let temp = JSON.parse(localStorage.getItem(JSON.stringify("salaryManagement")))
            temp.push(newObj)
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
            console.log("item", item)

            this.form.datas.splice(index, 1, item);
            localStorage.setItem(JSON.stringify("salaryManagement"), JSON.stringify(this.form.datas))

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
                    localStorage.setItem(JSON.stringify("salaryManagement"), JSON.stringify(this.form.datas))
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
