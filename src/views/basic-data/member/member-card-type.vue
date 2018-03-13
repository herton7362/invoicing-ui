<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <single-table ref="table"
                  :columns="table.columns"
                  form-title="会员卡类型维护"
                  domain-url="memberCardType"
                  :form-rule="form.rule"
                  :form-data="form.data"
                  :form-transform-response="formTransformResponse">
        <template slot="edit-form" slot-scope="props">
            <Row>
                <Col :span="12">
                <FormItem label="类型名称" prop="name">
                    <Input v-model="props.data.name" placeholder="请输入类型名称"/>
                </FormItem>
                </Col>
                <Col :span="12">
                <FormItem label="折扣" prop="discount">
                    <InputNumber v-model="props.data.discount" placeholder="0.9为9折"/>
                </FormItem>
                </Col>
            </Row>
            <FormItem label="备注" prop="remark">
                <Input v-model="props.data.remark" placeholder="请输入备注"/>
            </FormItem>
        </template>
    </single-table>
</template>

<script>
    import util from '@/libs/util';
    import SingleTable from '@/views/my-components/single-table/single-table.vue';

    export default {
        name: 'member-card-type',
        components: {
            SingleTable
        },
        data() {
            return {
                table: {
                    columns: [
                        {key:'name', title:'类型名称'},
                        {key:'discount', title:'折扣'},
                        {key:'remark', title:'备注'}
                    ]
                },
                form: {
                    rule: {
                        name: [
                            { required: true, message: '请填写类型名称', trigger: 'blur' }
                        ],
                        discount: [
                            { required: true, message: '请填写折扣', trigger: 'blur', type: 'number' },
                            { type: 'number', message: '必须为大于0小于1的小数', min: 0, max: 1, trigger: 'blur'}
                        ]
                    },
                    data: {
                        id: null,
                        name: null,
                        discount: 1,
                        remark: null
                    }
                }
            }
        },
        methods: {
            formTransformResponse(response) {
                response.data.password = null;
                return response;
            }
        },
        mounted() {
            this.$refs.table.loadGrid();
        }
    };
</script>