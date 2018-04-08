<style lang="less">
    @import '../../../styles/common.less';
    @import 'business-related-unit.less';
</style>

<template>
    <Card class="business-related-unit">
        <paged-table ref="table"
                      :columns="table.columns"
                      :actions="table.actions"
                      action-direction="vertical"
                      domain-url="businessRelatedUnit">
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

            <template slot="actions">
                <Button type="primary" icon="plus-round" @click="openNewModal"><span>新建</span></Button>
                <slot name="actions"/>
            </template>
        </paged-table>
        <edit-receivable-payable-amount
                ref="editReceivablePayableAmount"
                @on-save-success="reloadGrid"></edit-receivable-payable-amount>
        <edit-credit-line ref="editCreditLine" @on-save-success="reloadGrid"></edit-credit-line>
        <business-related-unit-form ref="saveForm" @on-save-success="onSaveSuccess"></business-related-unit-form>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import PagedTable from '@/views/my-components/single-table/paged-table.vue';
    import EditReceivablePayableAmount from './edit-receivable-payable-amount.vue';
    import EditCreditLine from './edit-credit-line.vue';
    import BusinessRelatedUnitForm from './business-related-unit-form.vue';

    export default {
        components: {
            PagedTable,
            EditReceivablePayableAmount,
            EditCreditLine,
            BusinessRelatedUnitForm
        },
        data() {
            return {
                table: {
                    columns: [
                        {title:'编号/名称', width: 200, render: (h, params) => {
                            return h('div', [
                                h('p', params.row.code),
                                h('p', {class: 'margin-top-small'}, params.row.name)
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
                                            type: 'log-in',
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
                                        h('span', '正常')
                                    ]);
                                }
                            }},
                    ],
                    actions: [
                        (h, params)=> {
                            return h('a', {
                                attrs: {
                                    href: 'javascript:void(0)'
                                },
                                style: {
                                    marginBottom: '5px',
                                    display: 'inline-block'
                                },
                                on: {
                                    click: () => {
                                        this.openEditModal(params.row);
                                    }
                                }
                            }, '修改')
                        },
                        (h, params)=> {
                            return h('Poptip',
                                {
                                    props: {
                                        confirm: true,
                                        transfer: true,
                                        title: '你确认删除这条内容吗？'
                                    },
                                    on: {
                                        'on-ok': () => {
                                            this.remove(params.row)
                                        }
                                    }
                                },
                                [
                                    h('a', {
                                        attrs: {
                                            href: 'javascript:void(0)'
                                        },
                                        style: {
                                            marginBottom: '5px',
                                            display: 'inline-block'
                                        }
                                    }, '删除')
                                ]
                            )
                        },
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
                }
            }
        },
        methods: {
            remove (row) {
                util.ajax.delete(`/api/businessRelatedUnit/${row.id}`).then(() => {
                    this.$Message.success('删除成功');
                    this.$refs.table.loadGrid();
                });
            },
            openNewModal() {
                this.$refs.saveForm.openNewModal();
            },
            openEditModal(row) {
                this.$refs.saveForm.openEditModal(row);
            },
            onSaveSuccess() {
                this.reloadGrid();
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