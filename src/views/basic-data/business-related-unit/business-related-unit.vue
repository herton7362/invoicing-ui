<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <single-table ref="table"
                      :columns="table.columns"
                      :actions="table.actions"
                      form-title="往来单位维护"
                      domain-url="businessRelatedUnit"
                      :modal-width="800"
                      :form-rule="form.rule"
                      :form-data="form.data">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="code" label="单位编号">
                    <Input v-model="props.params.code" placeholder="单位编号"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="name" label="单位名称">
                    <Input v-model="props.params.name" placeholder="单位名称"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="shortname" label="简名">
                    <Input v-model="props.params.shortname" placeholder="简名"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="pinyin" label="拼音码">
                    <Input v-model="props.params.pinyin" placeholder="拼音码"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="mobile" label="手机">
                    <Input v-model="props.params.mobile" placeholder="手机"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="telephone" label="电话">
                    <Input v-model="props.params.telephone" placeholder="电话"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="email" label="点子邮件">
                    <Input v-model="props.params.email" placeholder="点子邮件"/>
                </FormItem>
            </template>

            <template slot="edit-form" slot-scope="props">
                <FormItem label="单位名称" prop="name">
                    <Input v-model="props.data.name" placeholder="请输入单位名称" @on-blur="onNameBlur"/>
                </FormItem>
                <Row>
                    <Col :span="8">
                    <FormItem label="单位编号" prop="code">
                        <Input v-model="props.data.code" placeholder="请输入单位编号"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="单位简名" prop="shortname">
                        <Input v-model="props.data.shortname" placeholder="请输入单位简名"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col :span="8">
                    <FormItem label="拼音码" prop="pinyin">
                        <Input v-model="props.data.pinyin" placeholder="请输入拼音码"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="类型" prop="type">
                        <RadioGroup v-model="props.data.type">
                            <Radio label="VENDOR"><Icon type="android-car"></Icon> 供货商</Radio>
                            <Radio label="CUSTOMER"><Icon type="person-stalker"></Icon> 客户</Radio>
                        </RadioGroup>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="税号" prop="taxNumber">
                        <Input v-model="props.data.taxNumber" placeholder="请输入税号"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col :span="8">
                    <FormItem label="税率(%)" prop="taxRate">
                        <InputNumber v-model="props.data.taxRate" placeholder="请输入税率(%)"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="联系人" prop="linkman">
                        <Input v-model="props.data.linkman" placeholder="请输入联系人"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="电话" prop="telephone">
                        <Input v-model="props.data.telephone" placeholder="请输入电话"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col :span="8">
                    <FormItem label="手机" prop="mobile">
                        <Input v-model="props.data.mobile" placeholder="请输入手机"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="电子邮件" prop="email">
                        <Input v-model="props.data.email" placeholder="请输入电子邮件"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="价格等级" prop="priceLevel">
                        <Input v-model="props.data.priceLevel" placeholder="请输入价格等级"/>
                    </FormItem>
                    </Col>
                </Row>
                <FormItem label="地址" prop="detailAddress">
                    <Input v-model="props.data.detailAddress" placeholder="请输入地址"/>
                </FormItem>
                <Row>
                    <Col :span="16">
                    <FormItem label="开户银行" prop="depositBank">
                        <Input v-model="props.data.depositBank" placeholder="请输入开户银行"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="银行账号" prop="bankAccount">
                        <Input v-model="props.data.bankAccount" placeholder="请输入银行账号"/>
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
    import EditReceivablePayableAmount from './edit-receivable-payable-amount.vue';

    export default {
        components: {
            SingleTable
        },
        data() {
            return {
                table: {
                    columns: [
                        {key:'code', title:'单位编号'},
                        {key:'name', title:'单位名称'},
                        {title:'类型', align: 'center', render:(h, params) => {
                                if(params.row.type === 'CUSTOMER') {
                                    return h('Tooltip', {
                                        props: {
                                            content: '客户',
                                            placement: "top"
                                        }
                                    }, [
                                        h('Icon', {
                                            props: {
                                                type: 'person-stalker',
                                                size: 20
                                            }
                                        })
                                    ]);
                                } else{
                                    return  h('Tooltip', {
                                        props: {
                                            content: '供货商',
                                            placement: "top"
                                        }
                                    }, [
                                        h('Icon', {
                                            props: {
                                                type: 'android-car',
                                                size: 20
                                            }
                                        })
                                    ]);
                                }
                            }},
                        {key:'openingReceivableAmount', title:'期初应收款'},
                        {key:'openingPayableAmount', title:'期初应付款'},
                        {key:'nowReceivableAmount', title:'当前应收款'},
                        {key:'nowPayableAmount', title:'当前应付款'},
                        {key:'creditLine', title:'信用额度'},
                        {title:'可用额度'},
                        {key:'mobile', title:'手机'},
                        {key:'linkman', title:'联系人'},
                        {key:'priceLevel', title:'价格等级'},
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
                                                util.ajax.post(`/api/businessRelatedUnit/enable/${params.row.id}`).then((r)=>{
                                                    this.$Message.success('启用成功');
                                                    this.$refs.table.reloadGrid();
                                                });
                                            }
                                        }
                                    }, '启用'),
                                    h('DropdownItem', {
                                        nativeOn: {
                                            click:()=>{
                                                util.ajax.post(`/api/businessRelatedUnit/disable/${params.row.id}`).then((r)=>{
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
                                                    loading: true,
                                                    render: (h) => h(EditReceivablePayableAmount, {
                                                        props: {
                                                            openingReceivableAmount: params.row.openingReceivableAmount,
                                                            openingPayableAmount: params.row.openingPayableAmount
                                                        },
                                                        on: {
                                                            'on-openingReceivableAmount-change': (val)=>{
                                                                params.row.openingReceivableAmount = val;
                                                            },
                                                            'on-openingPayableAmount-change': (val)=>{
                                                                params.row.openingPayableAmount = val;
                                                            },
                                                            mounted (ref) {
                                                                params.row._EditReceivablePayableAmount = ref;
                                                            }
                                                        }
                                                    }),
                                                    onOk ()  {
                                                        params.row._EditReceivablePayableAmount.$refs.form.validate((valid) => {
                                                            if (valid) {
                                                                util.ajax.post(`/api/accountingSubject/editReceivablePayableAmount/${params.row.id}`, {
                                                                    openingReceivableAmount: params.row.openingReceivableAmount,
                                                                    openingPayableAmount: params.row.openingPayableAmount
                                                                }).then(()=>{
                                                                    this.$Message.success('操作成功');
                                                                    this.$refs.table.reloadGrid();
                                                                })
                                                            } else {
                                                                console.log(this)
                                                                this.loading = false;
                                                                params.row._EditReceivablePayableAmount.$nextTick(() => {
                                                                    //this.loading = true;
                                                                });
                                                            }
                                                        });
                                                    }
                                                })
                                            }
                                        }
                                    }, '修改期初应收应付款'),
                                ])
                            ])
                        }
                    ]
                },
                form: {
                    rule: {
                        name: [
                            { required: true, message: '请填写单位名称', trigger: 'blur' }
                        ],
                        code: [
                            { required: true, message: '请填写单位编码', trigger: 'blur' }
                        ],
                        pinyin: [
                            { required: true, message: '请填写拼音码', trigger: 'blur' }
                        ],
                        type: [
                            { required: true, message: '请选择类型', trigger: 'blur' }
                        ]
                    },
                    data: {
                        id: null,
                        name: null,
                        code: null,
                        shortname: null,
                        pinyin: null,
                        type: null,
                        taxNumber: null,
                        taxRate: 0,
                        linkman: null,
                        telephone: null,
                        mobile: null,
                        email: null,
                        priceLevel: null,
                        detailAddress: null,
                        depositBank: null,
                        bankAccount: null,
                        remark: null
                    }
                },
                roles: []
            }
        },
        methods: {
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