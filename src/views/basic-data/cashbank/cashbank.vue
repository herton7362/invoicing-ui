<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <single-table ref="table"
                      :columns="table.columns"
                      :actions="table.actions"
                      form-title="会计科目维护"
                      domain-url="accountingSubject"
                      :form-rule="form.rule"
                      :form-data="form.data"
                      :form-transform-response="formTransformResponse"
                      :default-query-params="table.defaultQueryParams">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="code" label="科目编号">
                    <Input v-model="props.params.code" placeholder="科目编号"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="name" label="科目名称">
                    <Input v-model="props.params.name" placeholder="科目名称"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="pinyin" label="拼音码">
                    <Input v-model="props.params.pinyin" placeholder="拼音码"/>
                </FormItem>
            </template>

            <template slot="edit-form" slot-scope="props">
                <FormItem label="科目名称" prop="name">
                    <Input v-model="props.data.name" placeholder="请输入科目名称" @on-blur="onNameBlur"/>
                </FormItem>
                <Row>
                    <Col :span="12">
                    <FormItem label="科目编号" prop="code">
                        <Input v-model="props.data.code" placeholder="请输入科目编号"/>
                    </FormItem>
                    </Col>
                    <Col :span="12">
                    <FormItem label="科目简名" prop="shortname">
                        <Input v-model="props.data.shortname" placeholder="请输入科目简名"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col :span="12">
                    <FormItem label="拼音码" prop="pinyin">
                        <Input v-model="props.data.pinyin" placeholder="请输入拼音码"/>
                    </FormItem>
                    </Col>
                </Row>
                <FormItem label="备注" prop="remark">
                    <Input v-model="props.data.remark" placeholder="请输入备注"/>
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
        data() {
            return {
                table: {
                    columns: [
                        {key:'code', title:'科目编号'},
                        {key:'name', title:'科目名称'},
                        {key:'shortname', title:'科目简名'},
                        {key:'pinyin', title:'拼音码'},
                        {key:'openingBalance', title:'期初余额'},
                        {key:'endingBalance', title:'期末余额'},
                        {title: '状态', render:(h, params) => {
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
                        {key:'remark', title:'备注'}
                    ],
                    actions: [
                        (h, params)=> {
                            return h('Dropdown', {
                                style: {
                                    textAlign: 'left'
                                }
                            }, [
                                h('a', {
                                    attrs: {
                                        href: 'javascript:void(0)'
                                    }
                                }, [
                                    h('span', '更多'),
                                    h('Icon', {props: {type: 'arrow-down-b'}})
                                ]),
                                h('DropdownMenu', {
                                    slot: 'list'
                                }, [
                                    h('DropdownItem', {
                                        nativeOn: {
                                            click:()=>{
                                                util.ajax.post(`/api/accountingSubject/enable/${params.row.id}`).then((r)=>{
                                                    this.$Message.success('启用成功');
                                                    this.$refs.table.reloadGrid();
                                                });
                                            }
                                        }
                                    }, '启用'),
                                    h('DropdownItem', {
                                        nativeOn: {
                                            click:()=>{
                                                util.ajax.post(`/api/accountingSubject/disable/${params.row.id}`).then((r)=>{
                                                    this.$Message.success('停用成功');
                                                    this.$refs.table.reloadGrid();
                                                });
                                            }
                                        }
                                    }, '停用'),
                                    h('DropdownItem', {
                                        nativeOn: {
                                            click:()=>{
                                                this.$Modal.confirm({
                                                    render: (h) => {
                                                        return h('InputNumber', {
                                                            props: {
                                                                value: params.row.openingBalance,
                                                                autofocus: true,
                                                                placeholder: '请填写期初金额'
                                                            },
                                                            style: {
                                                                width: '100%'
                                                            },
                                                            on: {
                                                                input: (val) => {
                                                                    params.row.openingBalance = val;
                                                                }
                                                            }
                                                        })
                                                    },
                                                    onOk: () => {
                                                        util.ajax.post(`/api/accountingSubject/openingBalance/${params.row.id}`, {
                                                            openingBalance: params.row.openingBalance
                                                        }).then(()=>{
                                                            this.$Message.success('操作成功');
                                                            this.$refs.table.reloadGrid();
                                                        })
                                                    }
                                                })
                                            }
                                        }
                                    }, '修改期初金额'),
                                    h('DropdownItem', {
                                        nativeOn: {
                                            click:()=>{
                                            }
                                        }
                                    }, '明细账本'),
                                    h('DropdownItem', {
                                        nativeOn: {
                                            click:()=>{
                                            }
                                        }
                                    }, '绑定支付宝'),
                                    h('DropdownItem', {
                                        nativeOn: {
                                            click:()=>{
                                            }
                                        }
                                    }, '绑定微信')
                                ])
                            ])
                        }
                    ],
                    defaultQueryParams: {
                        type: 'CASH_BANK'
                    }
                },
                form: {
                    rule: {
                        name: [
                            { required: true, message: '请填写科目名称', trigger: 'blur' }
                        ],
                        code: [
                            { required: true, message: '请填写科目编码', trigger: 'blur' }
                        ],
                        pinyin: [
                            { required: true, message: '请填写拼音码', trigger: 'blur' }
                        ]
                    },
                    data: {
                        id: null,
                        name: null,
                        code: null,
                        shortname: null,
                        pinyin: null,
                        remark: null,
                        type: 'CASH_BANK'
                    }
                }
            }
        },
        methods: {
            formTransformResponse(response) {
                response.data.password = null;
                return response;
            },
            onNameBlur() {
                let name = this.form.data.name;
                if(name) {
                    if(!this.form.data.shortname) {
                        this.form.data.shortname = name;
                    }
                    if(!this.form.data.pinyin) {
                        this.form.data.pinyin = util.getFirstPinyinLetter(name);
                    }
                }
            }
        },
        mounted() {
            this.$refs.table.loadGrid();
        }
    };
</script>