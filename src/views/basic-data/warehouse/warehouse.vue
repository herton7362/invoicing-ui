<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <paged-table ref="table"
                      domain-url="warehouse"
                      :columns="table.columns"
                      :actions="table.actions">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="code" label="仓库编号">
                    <Input v-model="props.params.code" placeholder="仓库编号"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="name" label="仓库名称">
                    <Input v-model="props.params.name" placeholder="仓库名称"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="shortname" label="仓库简名">
                    <Input v-model="props.params.shortname" placeholder="仓库简名"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="pinyin" label="拼音码">
                    <Input v-model="props.params.pinyin" placeholder="拼音码"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="logicallyDeleted" label="状态">
                    <RadioGroup v-model="props.params.logicallyDeleted">
                        <Radio :label="0">启用</Radio>
                        <Radio :label="1">停用</Radio>
                        <Radio label="">所有</Radio>
                    </RadioGroup>
                </FormItem>
                <FormItem class="padding-right-medium" prop="remark" label="备注">
                    <Input v-model="props.params.remark" placeholder="备注"/>
                </FormItem>
            </template>
            <template slot="actions">
                <Button type="primary" icon="plus-round" @click="openNewModal"><span>新建</span></Button>
                <slot name="actions"/>
            </template>
        </paged-table>
        <warehouse-form ref="saveForm" @on-save-success="onSaveSuccess"></warehouse-form>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import PagedTable from '@/views/my-components/single-table/paged-table.vue';
    import WarehouseForm from './warehouse-form.vue';

    export default {
        components: {
            PagedTable,
            WarehouseForm
        },
        data() {
            return {
                table: {
                    columns: [
                        {key: 'code', title: '仓库编号'},
                        {key: 'name', title: '仓库名称'},
                        {key: 'shortname', title: '仓库简名'},
                        {key: 'pinyin', title: '拼音码'},
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
                                        h('span', '正常')
                                    ]);
                                }
                            }},
                        {key: 'remark', title: '备注'}
                    ],
                    actions: [
                        (h, params)=> {
                            return h('a', {
                                attrs: {
                                    href: 'javascript:void(0)'
                                },
                                style: {
                                    marginRight: '5px',
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
                                            marginRight: '5px',
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
                                                util.ajax.post(`/api/warehouse/enable/${params.row.id}`).then((r)=>{
                                                    this.$Message.success('启用成功');
                                                    this.$refs.table.reloadGrid();
                                                });
                                            }
                                        }
                                    }, '启用'),
                                    h('DropdownItem', {
                                        nativeOn: {
                                            click:()=>{
                                                util.ajax.post(`/api/warehouse/disable/${params.row.id}`).then((r)=>{
                                                    this.$Message.success('停用成功');
                                                    this.$refs.table.reloadGrid();
                                                });
                                            }
                                        }
                                    }, '停用')
                                ])
                            ])
                        }
                    ]
                }
            }
        },
        methods: {
            remove (row) {
                util.ajax.delete(`/api/warehouse/${row.id}`).then(() => {
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
                this.$refs.table.loadGrid();
            }
        },
        mounted() {
            this.$refs.table.loadGrid();
        }
    };
</script>