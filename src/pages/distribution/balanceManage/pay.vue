<template>
	<section>
        <navigation></navigation>
		<div class="tabel-con">
            <BalanceTable :listContent="listContent" :step="step"></BalanceTable>
            
            <div class="examineSubmit">
                <el-col :span="12">
                    <el-form :model="addForm" label-width="120px" :rules="addFormRules" ref="addForm">
                        <el-form-item label="实际付款金额" prop="changeMoney">
                            <el-input type="number" v-model.trim="addForm.changeMoney" auto-complete="off" placeholder="请输入" ></el-input>
                        </el-form-item>
                        <el-form-item label="备注">
                            <el-input
                                type="textarea"
                                :rows="4"
                                placeholder="请输入付款流水号及相关备注"
                                v-model.trim="addForm.remarks">
                            </el-input>
                        </el-form-item>
                        <el-form-item label="">
                            <el-button size="mini" type="warning" @click="handleSubmit" :loading="addLoading">确认提交</el-button>
                            <go-back></go-back>                          
                        </el-form-item> 
                    </el-form>
                </el-col>                
            </div>
        </div>
    </section>
</template>

<script lang="ts">
    import {Component, Prop, Vue} from 'vue-property-decorator';
    import {getPutForwardManageExamine} from '@/api/api';
    import BalanceTable from './common/balanceTable.vue';

    @Component({
        components: {
            BalanceTable,
        },
    })
    export default class BalanceManagePay extends Vue {
        // loading
        private addLoading: boolean = false;
        // 列表数据
        private listContent: any[] = [];
        private step: any[] = [];
        private addFormRules: any = {
            changeMoney: [
                {required: true, message: '请输入实际付款金额', trigger: 'change'}
            ]
        };
        // 新增界面数据
        private addForm: any = {
            changeMoney: null,
            status: null,
            remarks: '',
        };

        // 获取数据
        private getList() {
            this.listContent = [];
            const para: any = {id: this.$route.params.id};
            getPutForwardManageExamine(para).then((res: any) => {
                const {code, data, msg} = res.data;

                if (data && data !== null) {
                    this.listContent.push(data);
                    this.step = data.step;
                } else {
                    this.listContent = [];
                    this.step = [];
                }
            }).catch((err: any) => {
            }); 
        }

        // 提交
        private handleSubmit() {
            // @ts-ignore
            this.$refs.addForm.validate((valid: any) => {
                if (valid) {
                    this.$confirm('确认提交吗？', '提示', {}).then(() => {
                        this.addLoading = true;
                        const para = Object.assign({}, this.addForm);
                        getPutForwardManageExamine(para).then((res: any) => {
                            const {code, msg} = res.data;
                            this.$message.success('提交成功!');
                            this.getList();
                            this.addLoading = false;
                        });
                    });
                }
            });
        }

        private mounted() {
            this.getList();
        }
    }
</script>

<style scoped lang="scss">
</style>
