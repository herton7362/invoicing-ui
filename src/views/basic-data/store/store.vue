<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <single-table ref="table"
                      :columns="table.columns"
                      form-title="门店维护"
                      domain-url="store"
                      :form-rule="form.rule"
                      :form-data="form.data">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="name" label="门店名称">
                    <Input v-model="props.params.name" placeholder="门店名称"/>
                </FormItem>
            </template>

            <template slot="edit-form" slot-scope="props">
                <Card dis-hover>
                    <span slot="title">
                        基本信息
                    </span>
                    <Row>
                        <Col :span="8">
                        <FormItem label="门店名称" prop="name">
                            <Input v-model="props.data.name" placeholder="门店名称"/>
                        </FormItem>
                        </Col>
                        <Col :span="8">
                        <FormItem label="门店编码" prop="code">
                            <Input v-model="props.data.code" placeholder="门店编码"/>
                        </FormItem>
                        </Col>
                        <Col :span="8">
                        <FormItem label="选择仓库" prop="warehouseId">
                            <Select v-model="props.data.warehouseId" placeholder="选择仓库">

                            </Select>
                        </FormItem>
                        </Col>
                    </Row>
                </Card>
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
        data() {
            return {
                table: {
                    columns: [
                        {key: 'name', title: '门店名称'},
                        {title: '店长'},
                        {key: 'startBusinessTime', title: '开始营业时间'},
                        {key: 'closeBusinessTime', title: '关闭营业时间'},
                        {title: '门店状态', render:(h, params) => {
                                if(params.row.logicallyDeleted) {
                                    return h('div', [
                                        h('Icon', {
                                            props: {
                                                type: 'record',
                                                color: '#ccc'
                                            },
                                            style: {
                                                marginRight: '5px'
                                            }
                                        }),
                                        h('span', '停用')
                                    ]);
                                } else{
                                    return h('div', [
                                        h('Icon', {
                                            props: {
                                                type: 'record',
                                                color: '#52c41a'
                                            },
                                            style: {
                                                marginRight: '5px'
                                            }
                                        }),
                                        h('span', '启用')
                                    ]);
                                }
                            }},
                        {key: 'linkman', title: '联系人'},
                        {key: 'code', title: '门店编码'}
                    ]
                },
                form: {
                    rule: {
                    },
                    data: {
                        id: null,
                        name: null,
                        code: null,
                        warehouseId: null,

                    }
                }
            }
        },
        methods: {
        },
        mounted() {
            this.$refs.table.loadGrid();
        }
    };
</script>