<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <div>
        <paged-table ref="table"
                     :show-header="showHeader"
                     :query-params="queryParams"
                     :domain-url="domainUrl"
                     :actions="table.actions"
                     :default-query-params="defaultQueryParams"
                     :table-transform-response="tableTransformResponse"
                     :table-transform-query-params="tableTransformQueryParams"
                     :search-able="table.searchAble"
                     :columns="columns"
                     @on-load="onLoadGrid"
                     :height="height"
                     :width="width"
                     :page="page"
                     :action-direction="actionDirection">
            <template slot="query-form" slot-scope="props">
                <slot name="query-form" :params="props.params">

                </slot>
            </template>

            <template slot="actions">
                <Button type="primary" icon="plus-round" @click="openNewModal"><span>新建</span></Button>
                <slot name="actions"/>
            </template>
        </paged-table>
        <save-form ref="saveForm"
                   :domain-url="domainUrl"
                   :form-title="formTitle"
                   :mask-closable="maskClosable"
                   :modal-width="modalWidth"
                   :form-rule="formRule"
                   :form-data="formData"
                   :form-transform-response="formTransformResponse"
                   :form-transform-data="formTransformData"
                   :label-width="labelWidth"
                   @on-save-success="onSaveSuccess">
            <template slot="edit-form" slot-scope="props">
                <slot name="edit-form" :data="props.data"/>
            </template>
        </save-form>
    </div>
</template>

<script>
    import util from '@/libs/util';
    import fold from '@/views/my-components/fold/fold.vue';
    import PagedTable from './paged-table.vue';
    import SaveForm from './save-form.vue';

    export default {
        name: 'single-table',
        components: {
            fold,
            PagedTable,
            SaveForm
        },
        props: {
            columns: {
                type: Array,
                default() {
                    return [];
                }
            },
            actions: {
                type: Array,
                default() {
                    return [];
                }
            },
            domainUrl: String,
            modalWidth: Number,
            formTitle: {
                type: String,
                default: '窗口'
            },
            formRule: {
                type: Object,
                default() {
                    return {};
                }
            },
            formData: {
                type: Object,
                default() {
                    return {};
                }
            },
            queryParams: {
                type: Object,
                default() {
                    return {};
                }
            },
            defaultQueryParams: {
                type: Object,
                default() {
                    return {};
                }
            },
            formTransformResponse: {
                type: Function,
                default(response) {
                    return response
                }
            },
            tableTransformResponse: {
                type: Function,
                default(response) {
                    return response
                }
            },
            formTransformData: {
                type: Function,
                default(data) {
                    return data
                }
            },
            tableTransformQueryParams: {
                type: Function,
                default(data) {
                    return data
                }
            },
            maskClosable: {
                type: Boolean,
                default: true
            },
            labelWidth: {
                type: Number,
                default: 80
            },
            height: Number,
            width: Number,
            page: {
                type: Boolean,
                default: true
            },
            showHeader: {
                type: Boolean,
                default: true
            },
            actionDirection: {
                type: String,
                validator(val) {
                    return 'horizontal' === val || 'vertical' === val;
                },
                default: 'horizontal'
            }
        },
        data() {
            return {
                table: {
                    actions: [
                        (h, params)=> {
                            return h('a', {
                                attrs: {
                                    href: 'javascript:void(0)'
                                },
                                style: {
                                    ['margin' + (this.actionDirection === 'horizontal'? 'Right': 'Bottom')]: '5px',
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
                                            ['margin' + (this.actionDirection === 'horizontal'? 'Right': 'Bottom')]: '5px',
                                            display: 'inline-block'
                                        }
                                    }, '删除')
                                ]
                            )
                        },
                        ...this.actions
                    ],
                    searchAble: true
                },
                form: {
                    modal: false,
                    loading: true,
                    data: this.formData
                }
            }
        },
        methods: {
            remove (row) {
                util.ajax.delete(`/api/${this.domainUrl}/${row.id}`).then(() => {
                    this.$Message.success('删除成功');
                    this.loadGrid();
                });
            },
            openNewModal() {
                this.$refs.saveForm.openNewModal();
                this.$emit('on-new-modal-open');
            },
            openEditModal(row) {
                this.$refs.saveForm.openEditModal(row);
                this.$emit('on-edit-modal-open');
            },
            onSaveSuccess() {
                this.loadGrid();
            },
            resetQueryForm () {
                this.$refs.table.resetQueryForm();
            },
            loadGrid ({
                          silent = false // 不触发事件
            } = {}) {
                this.$refs.table.loadGrid({silent});
            },
            reloadGrid({
                           silent = false // 不触发事件
                       } = {}) {
                this.$refs.table.loadGrid({silent});
            },
            clearData() {
                this.$refs.table.clearData();
            },
            onLoadGrid() {
                this.$emit('on-load');
            }
        },
        mounted() {
            if(!this.$scopedSlots['query-form']) {
                this.table.searchAble = false;
            }
        }
    };
</script>