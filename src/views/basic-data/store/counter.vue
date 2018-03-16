<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card dis-hover style="max-width: 600px;">
        <single-table ref="table"
                      :page="false"
                      :columns="table.columns"
                      form-title="收银台维护"
                      domain-url="storeCounter"
                      :form-rule="form.rule"
                      :form-data="form.data"
                      :default-query-params="table.defaultQueryParams">
            <template slot="edit-form" slot-scope="props">
                <FormItem label="名称" prop="name">
                    <Input v-model="props.data.name" placeholder="请输入名称"/>
                </FormItem>
                <FormItem label="编号" prop="code">
                    <Input v-model="props.data.code" placeholder="请输入编号"/>
                </FormItem>
            </template>
        </single-table>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import SingleTable from '@/views/my-components/single-table/single-table.vue';

    export default {
        components: {
            SingleTable
        },
        props: {
            store: Object,
        },
        data() {
            return {
                table: {
                    columns: [
                        {key:'code', title:'收银台编号'},
                        {key:'name', title:'收银台名称'}
                    ],
                    defaultQueryParams: {
                        storeId: null
                    }
                },
                form: {
                    rule: {
                        name: [
                            { required: true, message: '请填写名称', trigger: 'blur' }
                        ],
                        code: [
                            { required: true, message: '请填写编号', trigger: 'blur' }
                        ]
                    },
                    data: {
                        id: null,
                        name: null,
                        code: null,
                        storeId: null
                    }
                }
            }
        },
        methods: {
        },
        mounted() {
            this.form.data.storeId = this.store.id;
            this.table.defaultQueryParams.storeId = this.store.id;
            this.$refs.table.loadGrid();
        }
    };
</script>