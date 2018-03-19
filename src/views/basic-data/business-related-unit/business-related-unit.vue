<style lang="less">
    @import '../../../styles/common.less';
    @import 'business-related-unit.less';
</style>

<template>
    <Card class="business-related-unit">
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
        <edit-receivable-payable-amount
                ref="editReceivablePayableAmount"
                @on-save-success="reloadGrid"></edit-receivable-payable-amount>
        <edit-credit-line ref="editCreditLine" @on-save-success="reloadGrid"></edit-credit-line>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import SingleTable from '@/views/my-components/single-table/single-table.vue';
    import EditReceivablePayableAmount from './edit-receivable-payable-amount.vue';
    import EditCreditLine from './edit-credit-line.vue';

    export default {
        components: {
            SingleTable,
            EditReceivablePayableAmount,
            EditCreditLine
        },
        data() {
            return {
                table: {
                    columns: [
                        {title:'单位名称', width: 200, render: (h, params) => {
                            return h('div', [
                                h('p', params.row.code),
                                h('p', params.row.name)
                            ])
                        }},
                        {title: ' ', width: 80, align: 'center', render: (h, params) => {
                            if(params.row.type === 'CUSTOMER') {
                                return h('div', [
                                    h('Icon', {
                                        props: {
                                            type: 'person-stalker',
                                            size: 20
                                        }
                                    }),
                                    h('p', {class: 'margin-top-small'}, '客户')
                                ]);
                            } else{
                                return h('Tooltip', [
                                    h('Icon', {
                                        props: {
                                            type: 'android-car',
                                            size: 20
                                        }
                                    }),
                                    h('p', {class: 'margin-top-small'}, '供货商')
                                ]);
                            }
                        }},
                        {key:'openingReceivableAmount', title:'应收/应付款', align: 'center', render: (h, params) => {
                            return h('div', {class: 'amount-list'}, [
                                h('div', {class: 'amount'}, [
                                    h('div', {class: 'mini-counts'}, params.row.openingReceivableAmount),
                                    h('div', [
                                        h('span', {class: 'text-highlight'}, '期初'),
                                        h('span', '应收'),
                                    ])
                                ]),
                                h('div', {class: 'amount'}, [
                                    h('div', {class: 'mini-counts'}, params.row.nowReceivableAmount),
                                    h('div', [
                                        h('span', {class: 'text-highlight'}, '当前'),
                                        h('span', '应收'),
                                    ])
                                ]),
                                h('div', {class: 'amount'}, [
                                    h('div', {class: 'mini-counts'}, params.row.openingPayableAmount),
                                    h('div', [
                                        h('span', {class: 'text-highlight'}, '期初'),
                                        h('span', '应付'),
                                    ])
                                ]),
                                h('div', {class: 'amount'}, [
                                    h('div', {class: 'mini-counts'}, params.row.nowPayableAmount),
                                    h('div', [
                                        h('span', {class: 'text-highlight'}, '当前'),
                                        h('span', '应付'),
                                    ])
                                ]),
                            ])
                        }},
                        {title:'信用额度', width: 180, align: 'center', render:(h, params) => {
                            if(params.row.creditLineEnable) {
                                let available = params.row.creditLine - params.row.nowReceivableAmount;
                                let usedRate = (params.row.nowReceivableAmount / params.row.creditLine) * 100;
                                let availableRate = (available / params.row.creditLine) * 100;
                                if(availableRate < 0) {
                                    availableRate = 0;
                                    usedRate = 100;
                                }
                                return h('i-circle', {
                                    props: {
                                        size: 140,
                                        percent: usedRate,
                                        strokeColor: available / params.row.creditLine < 0.1? '#ff5500': '#43a3fb'
                                    },
                                    style: {
                                        margin: '10px 0'
                                    }
                                }, [
                                    h('p', `可用额度`),
                                    h('p', {
                                        class: ['available-amount', 'margin-top-small'],
                                        style: `color: ${available < 0?'#ff5500': ''}`
                                    }, available),
                                    h('p', {class: 'margin-top-small'}, `剩余${availableRate}%`),
                                    h('Button', {
                                        props: {
                                            size: 'small',
                                            type: 'ghost',
                                            shape: 'circle'
                                        },
                                        class: 'margin-top-small',
                                        on: {
                                            click: ()=> {
                                                this.$refs.editCreditLine.open(params.row);
                                            }
                                        }
                                    }, '设置')
                                ])
                            } else {
                                return h('div', [
                                    h('p', {style: 'color: #999'}, '未启用'),
                                    h('Button', {
                                        props: {
                                            size: 'small',
                                            type: 'ghost',
                                            shape: 'circle'
                                        },
                                        class: 'margin-top-small',
                                        on: {
                                            click: ()=> {
                                                this.$refs.editCreditLine.open(params.row);
                                            }
                                        }
                                    }, '设置')

                                ])
                            }
                        }},
                        {title:'联系方式', render:(h, params) => {
                            const labels = [];
                            if(params.row.linkman) {
                                labels.push(h('span', {class: 'nowrap'}, params.row.linkman));
                            }

                            if(params.row.mobile || params.row.telephone) {
                                const tel = [];
                                tel.push(h('p', {class: 'nowrap'}, 'Tel: '));
                                if(params.row.mobile) {
                                    tel.push(h('p', {class: 'nowrap'}, params.row.mobile));
                                }
                                if(params.row.telephone) {
                                    tel.push(h('p', {class: 'nowrap'}, params.row.telephone));
                                }
                                labels.push(h('p', {class: 'nowrap'}, tel));
                            }

                            if(params.row.telephone) {
                                labels.push(h('p', {class: 'nowrap'}, `E-mail: ${params.row.email}`));
                            }
                            return h('div', {class: 'link-type'}, labels)
                        }},
                        {key:'priceLevel', width: 120, title:'价格等级'},
                        {title: '状态', width: 80, render:(h, params) => {
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
                                                this.$refs.editReceivablePayableAmount.open(params.row);
                                            }
                                        }
                                    }, '修改期初应收应付款'),
                                    h('DropdownItem', {
                                        nativeOn: {
                                            click:()=>{
                                                this.$refs.editCreditLine.open(params.row);
                                            }
                                        }
                                    }, '设置信用额度')
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
            },
            reloadGrid() {
                this.$refs.table.reloadGrid();
            }
        },
        mounted() {
            this.$refs.table.loadGrid();
        }
    };
</script>