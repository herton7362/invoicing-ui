<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Modal v-model="form.modal"
           title="信用额度设置"
           :loading="form.loading"
           @on-ok="save"
           :width="400">
        <Form ref="form" :model="form.data" :rules="form.rule" :label-width="90">
            <FormItem prop="creditLineEnable">
                <Checkbox v-model="form.data.creditLineEnable">启用信用额度管理</Checkbox>
            </FormItem>
            <FormItem label="信用额度" prop="creditLine">
                <InputNumber v-model="form.data.creditLine" placeholder="请输入信用额度" :disabled="!form.data.creditLineEnable" style="width: 100%;"/>
            </FormItem>
        </Form>
    </Modal>
</template>

<script>
    import util from '@/libs/util';

    export default {
        data() {
            return {
                form: {
                    modal: false,
                    loading: true,
                    data: {
                        id: null,
                        creditLineEnable: null,
                        creditLine: null
                    },
                    rule: {
                        creditLine: [
                            { type: 'number', required: true, message: '请填写信用额度', trigger: 'blur' }
                        ]
                    }
                }
            }
        },
        methods: {
            open(row) {
                this.form.data = row;
                this.form.modal = true;
            },
            save() {
                this.$refs.form.validate((valid) => {
                    if(this.form.data.creditLineEnable && this.form.data.creditLine < this.form.data.nowReceivableAmount) {
                        this.$Message.warning(`信用额度不能小于单位的应收账款[${this.form.data.nowReceivableAmount}]`);
                        valid = false;
                    }
                    if (valid) {
                        util.ajax.post(`/api/businessRelatedUnit/editCreditLine/${this.form.data.id}`, {
                            creditLineEnable: this.form.data.creditLineEnable,
                            creditLine: this.form.data.creditLine
                        }).then(()=>{
                            this.$Message.success('操作成功');
                            this.form.modal = false;
                            this.$emit('on-save-success');
                        })
                    } else {
                        this.form.loading = false;
                        this.$nextTick(() => {
                            this.form.loading = true;
                        });
                    }
                });
            }
        }
    };
</script>