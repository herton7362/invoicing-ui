<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Modal v-model="form.modal"
           title="期初应收应付款"
           :loading="form.loading"
           @on-ok="save"
           :width="400">
        <Form ref="form" :model="form.data" :rules="form.rule" :label-width="90">
            <FormItem label="期初应收款" prop="openingReceivableAmount">
                <InputNumber v-model="form.data.openingReceivableAmount" autofocus placeholder="请输入期初应收款" style="width: 100%;"/>
            </FormItem>
            <FormItem label="期初应付款" prop="openingPayableAmount">
                <InputNumber v-model="form.data.openingPayableAmount" placeholder="请输入期初应付款" style="width: 100%;"/>
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
                        openingReceivableAmount: null,
                        openingPayableAmount: null
                    },
                    rule: {
                        openingReceivableAmount: [
                            { type: 'number', required: true, message: '请填写期初应收款', trigger: 'blur' }
                        ],
                        openingPayableAmount: [
                            { type: 'number', required: true, message: '请填写期初应付款', trigger: 'blur' }
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
                    if (valid) {
                        util.ajax.post(`/api/businessRelatedUnit/editReceivablePayableAmount/${this.form.data.id}`, {
                            openingReceivableAmount: this.form.data.openingReceivableAmount,
                            openingPayableAmount: this.form.data.openingPayableAmount
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