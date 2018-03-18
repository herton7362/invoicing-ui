<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Form ref="form" :model="form.data" :rules="form.rule" :label-width="90">
        <FormItem label="期初应收款" prop="openingReceivableAmount">
            <InputNumber v-model="form.data.openingReceivableAmount" placeholder="请输入期初应收款" style="width: 100%;"/>
        </FormItem>
        <FormItem label="期初应付款" prop="openingPayableAmount">
            <InputNumber v-model="form.data.openingPayableAmount" placeholder="请输入期初应付款" style="width: 100%;"/>
        </FormItem>
    </Form>
</template>

<script>
    import util from '@/libs/util';

    export default {
        props: {
            openingReceivableAmount: Number,
            openingPayableAmount: Number
        },
        data() {
            return {
                form: {
                    data: {
                        openingReceivableAmount: this.openingReceivableAmount,
                        openingPayableAmount: this.openingPayableAmount
                    },
                    rule: {
                        openingReceivableAmount: [
                            { required: true, message: '请填写期初应收款', trigger: 'blur' }
                        ],
                        openingPayableAmount: [
                            { required: true, message: '请填写期初应付款', trigger: 'blur' }
                        ]
                    }
                }
            }
        },
        methods: {
        },
        mounted() {
            this.$emit('mounted', this);
        },
        watch: {
            'form.openingReceivableAmount'(val) {
                this.$emit('on-openingReceivableAmount-change', val);
            },
            'form.openingPayableAmount'(val) {
                this.$emit('on-openingPayableAmount-change', val);
            }
        }
    };
</script>