<style lang="less">
    @import './property-value.less';
</style>

<template>
    <div class="goods-property-value-main">
        <div class="goods-property-value-tree">
            <Card>
                <p slot="title">
                    商品属性
                </p>
                <editable-tree ref="goodsProperty"
                               domainUrl="goodsProperty"
                               root-name="全部属性"
                               @on-select-change="onSelectTree"></editable-tree>
            </Card>
        </div>
        <div class="goods-property-value-list">
            <Card>
                <p slot="title">
                    {{tree.selected.title}}属性值
                </p>
                <property-group ref="propertyGroup" :goods-property="tree.selected" @on-check="onTagCheck"></property-group>
                <single-table class="margin-top-medium"
                              ref="table"
                              :columns="table.columns"
                              :query-params="table.queryParams"
                              form-title="商品属性值维护"
                              domain-url="goodsPropertyValue"
                              :form-rule="form.rule"
                              :form-data="form.data"
                              :form-transform-response="formTransformResponse"
                              @on-new-modal-open="onNewModalOpen">
                    <template slot="edit-form" slot-scope="props">
                        <FormItem label="属性名称" prop="name">
                            <Input v-model="props.data.name" placeholder="请输入属性名称"/>
                        </FormItem>
                        <FormItem label="属性条码" prop="barcode">
                            <Input v-model="props.data.barcode" placeholder="请输入属性条码"/>
                        </FormItem>
                        <FormItem label="备注" prop="remark">
                            <Input v-model="props.data.remark" placeholder="请输入备注"/>
                        </FormItem>
                        <FormItem label="组" prop="goodsPropertyCategoryId">
                            <CheckboxGroup v-model="form.data.goodsPropertyCategoryId">
                                <Checkbox :key="item.id" :label="item.id" v-for="item in group.data">{{item.name}}</Checkbox>
                            </CheckboxGroup>
                        </FormItem>
                    </template>
                </single-table>
            </Card>
        </div>
    </div>
</template>

<script>
    import util from '@/libs/util';
    import singleTable from '@/views/my-components/single-table/single-table.vue';
    import EditableTree from '@/views/my-components/editable-tree/editable-tree.vue'
    import PropertyGroup from './group.vue';

    export default {
        components: {
            singleTable,
            EditableTree,
            PropertyGroup
        },
        data() {
            return {
                tree: {
                    selected: {}
                },
                group: {
                    data: []
                },
                table: {
                    columns: [
                        {key: 'barcode', title: '条码名称'},
                        {key: 'name', title: '属性名称'},
                        {key: 'goodsPropertyCategoryId', title: '组'},
                        {key: 'remark', title: '备注'}
                    ],
                    queryParams: {}
                },
                form: {
                    rule: {
                        name: [
                            { required: true, message: '请填写属性名称', trigger: 'blur' }
                        ]
                    },
                    data: {
                        id: null,
                        name: null,
                        barcode: null,
                        goodsPropertyCategoryId: null,
                        remark: null,
                        goodsPropertyId: null
                    }
                }
            }
        },
        methods: {
            onSelectTree(nodes) {
                if(nodes && nodes.length > 0) {
                    this.tree.selected = nodes[0];
                }
                this.loadGrid();
                this.$refs.propertyGroup.$nextTick(()=>{
                    this.$refs.propertyGroup.loadGroup();
                });
                this.$nextTick(()=>{
                    this.loadGroup();
                })
            },
            formTransformResponse(response) {
                return response;
            },
            loadGrid() {
                this.table.queryParams.goodsPropertyId = this.tree.selected.id;
                this.$refs.table.$refs.table.loadGrid({
                    silent: true
                });
            },
            loadTreeData() {
                return this.$refs.goodsProperty.loadTreeData();
            },
            onNewModalOpen() {
                this.$refs.table.form.data.goodsPropertyId = this.tree.selected.id;
            },
            onTagCheck(checked) {
                this.table.queryParams.goodsPropertyCategoryId = checked.join(',');
                this.$refs.table.$refs.table.loadGrid({
                    silent: true
                });
            },
            loadGroup() {
                util.ajax.get('/api/goodsPropertyCategory', {
                    params: {
                        goodsPropertyId: this.tree.selected.id
                    }
                }).then((response)=>{
                    this.group.data = response.data;
                })
            }
        },
        mounted() {
            this.loadTreeData().then((response)=>{
                if(response.data[0]) {
                    this.$refs.goodsProperty.selectNode(response.data[0].id);
                } else {
                    this.loadGrid();
                }
            });
        }
    };
</script>
