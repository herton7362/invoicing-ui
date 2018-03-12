<style lang="less">
    @import '../../../styles/common.less';
    @import 'member-card.less';
</style>

<template>
    <div>
        <Row>
            <Button type="primary" icon="plus-round">添加</Button>
        </Row>
        <Row class="margin-top-medium">
            <Table :columns="table.columns"
                   :data="data">
            </Table>
        </Row>
    </div>
</template>

<script>
    export default {
        name: 'member-card',
        props: {
            data: {
                type: Array,
                default() {
                    return [];
                }
            }
        },
        data() {
            return {
                table: {
                    columns: [
                        {key: 'cardNumber', title: '卡号'},
                        {key: 'memberCardTypeId', title: '类型', width: 80},
                        {key: 'balance', align: 'right', title: '余额', width: 90},
                        {key: 'points', align: 'right', title: '积分', width: 90},
                        {key: 'discount', align: 'center', title: '折扣', width: 60},
                        {key: 'createdDate', title: '发卡日期', width: 100},
                        {key: 'expireDate', title: '到期日期', width: 100},
                        {key: 'remark', title: '备注'},
                        {title: '操作', width: '100', align: 'center', render: (h, params) => {
                            return h('div', [
                                h('a', {
                                    attrs: {
                                        href: 'javascript:void(0)'
                                    },
                                    style: {
                                        marginRight: '5px'
                                    },
                                    on: {
                                        click: () => {
                                        }
                                    }
                                }, '修改'),
                                h('Poptip',
                                    {
                                        props: {
                                            confirm: true,
                                            transfer: true,
                                            title: '您确认删除这条内容吗？'
                                        },
                                        on: {
                                            'on-ok': () => {
                                            }
                                        }
                                    },
                                    [
                                        h('a', {
                                            attrs: {
                                                href: 'javascript:void(0)'
                                            },
                                            style: {
                                                marginRight: '5px'
                                            }
                                        }, '删除')
                                    ]
                                ),
                                h('Dropdown', {
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
                                                }
                                            }
                                        }, '启用'),
                                        h('DropdownItem', {
                                            nativeOn: {
                                                click:()=>{
                                                }
                                            }
                                        }, '停用')
                                    ])
                                ])
                            ])
                        }}
                    ]
                }
            }
        }
    }
</script>