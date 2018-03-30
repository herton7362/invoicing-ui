<style lang="less">
    @import './goods.less';
</style>

<template>
    <div class="goods-main">
        <div class="goods-tree">
            <Card>
                <editable-tree ref="goodsCategory"
                               :selected-id="null"
                               data-type="tree"
                               domainUrl="goodsCategory"
                               root-name="全部分类"
                               @on-select-change="onSelectTree"
                               :tree-transform-response="treeTransformResponse"></editable-tree>
            </Card>
        </div>
        <Card class="goods-list">
            <single-table ref="table"
                          :show-header="false"
                          :columns="table.columns"
                          :query-params="table.queryParams"
                          :actions="table.actions"
                          action-direction="vertical"
                          form-title="商品维护"
                          domain-url="goods"
                          :form-rule="form.rule"
                          :form-data="form.data"
                          :modal-width="700"
                          @on-new-modal-open="onNewModalOpen"
                          @on-edit-modal-open="onEditModalOpen"
                          @on-data-loaded="onDataLoaded">
                <template slot="query-form" slot-scope="props">
                    <FormItem class="padding-right-medium" prop="name" label="商品名称">
                        <Input v-model="props.params.name" placeholder="商品名称"/>
                    </FormItem>
                    <FormItem class="padding-right-medium" prop="code" label="商品编码">
                        <Input v-model="props.params.code" placeholder="商品编码"/>
                    </FormItem>
                    <FormItem class="padding-right-medium" prop="shortname" label="商品简称">
                        <Input v-model="props.params.shortname" placeholder="商品简称"/>
                    </FormItem>
                    <FormItem class="padding-right-medium" prop="pinyin" label="拼音码">
                        <Input v-model="props.params.pinyin" placeholder="拼音码"/>
                    </FormItem>
                    <FormItem class="padding-right-medium" prop="standard" label="规格">
                        <Input v-model="props.params.standard" placeholder="规格"/>
                    </FormItem>
                    <FormItem class="padding-right-medium" prop="model" label="型号">
                        <Input v-model="props.params.model" placeholder="型号"/>
                    </FormItem>
                </template>

                <template slot="edit-form" slot-scope="props">
                    <Tabs type="card">
                        <TabPane label="基本信息">
                            <basic-form-content :form-data="props.data" :tree="categoryData"></basic-form-content>
                        </TabPane>
                        <TabPane label="售价管理" style="padding: 0 20px;">
                            <sale-price-form-content :form-data="props.data"></sale-price-form-content>
                        </TabPane>
                        <TabPane label="属性设置">
                            <property-form-content ref="propertyFormContent" :form-data="props.data"></property-form-content>
                        </TabPane>
                        <TabPane label="商品图片管理">
                            <image-form-content :form-data="props.data"></image-form-content>
                        </TabPane>
                    </Tabs>
                </template>
            </single-table>
        </Card>
    </div>
</template>

<script>
    import util from '@/libs/util';
    import SingleTable from '@/views/my-components/single-table/single-table.vue';
    import EditableTree from '@/views/my-components/editable-tree/editable-tree.vue'
    import BasicFormContent from './basic-form-content.vue';
    import SalePriceFormContent from './sale-price-form-content.vue';
    import PropertyFormContent from './property-form-content.vue';
    import ImageFormContent from './image-form-content.vue';

    export default {
        components: {
            PropertyFormContent,
            SingleTable,
            EditableTree,
            BasicFormContent,
            SalePriceFormContent,
            ImageFormContent
        },
        data() {
            return {
                tree: {
                    selected: {}
                },
                table: {
                    columns: [
                        {width: 100, render:(h, {row}) => {
                            return h('img', {
                                class: 'goods-table-cover',
                                attrs: {
                                    src: `${util.baseURL}/attachment/download/${row.goodsCoverImage.attachmentId? row.goodsCoverImage.attachmentId: 'none-cover'}`
                                }
                            })
                        }},
                        {render:(h, {row}) => {
                            let status;
                            if(row.logicallyDeleted) {
                                status = h('span', [
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
                                status = h('span', [
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
                            return h('div', [
                                h('p', `商品名称：${row.name}`),
                                h('p', `商品编码：${row.code}`),
                                h('p', [
                                    status
                                ])
                            ])
                        }},
                        {render:(h, {row}) => {
                            let retailPrice = null;
                            if(row.basicGoodsPrice.retailPrice) {
                                retailPrice = h('div', [
                                    h('label', {class: 'goods-table-title'}, '零售价：'),
                                    h('span', `￥${row.basicGoodsPrice.retailPrice}`)
                                ])
                            }
                            return h('div', [
                                h('div', [
                                    h('label', {class: 'goods-table-title'}, '利润：'),
                                    h('span', `￥${row.basicGoodsPrice.retailPrice - row.costPrice}`)
                                ]),
                                retailPrice
                            ])
                        }},
                        {render:(h, {row}) => {
                                return h('div', [
                                    h('div', [
                                        h('label', {class: 'goods-table-title'}, '重量：'),
                                        h('span', `${row.weight} kg`)
                                    ]),
                                    h('div', [
                                        h('label', {class: 'goods-table-title'}, '长度：'),
                                        h('span', row.length)
                                    ]),
                                    h('div', [
                                        h('label', {class: 'goods-table-title'}, '宽度：'),
                                        h('span', row.width)
                                    ]),
                                    h('div', [
                                        h('label', {class: 'goods-table-title'}, '高度：'),
                                        h('span', row.height)
                                    ])
                                ])
                            }},
                        {render:(h, {row}) => {
                            return h('div', [
                                h('p', `库存：10 ${row.basicGoodsPrice.unitName?row.basicGoodsPrice.unitName: ''}`),
                                h('p', [h('a', {
                                    attrs: {
                                        href: 'javascript:void(0)'
                                    }
                                }, '查看库存')])
                            ])
                        }}
                    ],
                    queryParams: {},
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
                                                util.ajax.post(`/api/goods/enable/${params.row.id}`).then((r)=>{
                                                    this.$Message.success('启用成功');
                                                    this.$refs.table.reloadGrid();
                                                });
                                            }
                                        }
                                    }, '启用'),
                                    h('DropdownItem', {
                                        nativeOn: {
                                            click:()=>{
                                                util.ajax.post(`/api/goods/disable/${params.row.id}`).then((r)=>{
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
                },
                form: {
                    rule: {
                        name: [
                            { required: true, message: '请填写商品名称', trigger: 'blur' }
                        ],
                        code: [
                            { required: true, message: '请填写商品编号', trigger: 'blur' }
                        ]
                    },
                    data: {
                        id: null,
                        goodsCategoryId: null,
                        name: null,
                        code: null,
                        shortname: null,
                        pinyin: null,
                        brandId: null,
                        madeAddress: null,
                        standard: null,
                        model: null,
                        weight: 0,
                        length: 0,
                        width: 0,
                        height: 0,
                        costPrice: 0,
                        remark: null,
                        basicGoodsPrice: {
                            id: null,
                            unitName: null,
                            conversionRelationship: null,
                            retailPrice: null,
                            presetPrice1: null,
                            presetPrice2: null,
                            presetPrice3: null,
                            presetPrice4: null,
                            presetPrice5: null,
                            presetPrice6: null,
                            presetPrice7: null,
                            presetPrice8: null,
                            presetPrice9: null,
                            presetPrice10: null
                        },
                        assistGoodsPrice1: {
                            id: null,
                            unitName: null,
                            conversionRelationship: null,
                            retailPrice: null,
                            retailPrice: null,
                            presetPrice1: null,
                            presetPrice2: null,
                            presetPrice3: null,
                            presetPrice4: null,
                            presetPrice5: null,
                            presetPrice6: null,
                            presetPrice7: null,
                            presetPrice8: null,
                            presetPrice9: null,
                            presetPrice10: null
                        },
                        assistGoodsPrice2: {
                            id: null,
                            unitName: null,
                            conversionRelationship: null,
                            retailPrice: null,
                            retailPrice: null,
                            presetPrice1: null,
                            presetPrice2: null,
                            presetPrice3: null,
                            presetPrice4: null,
                            presetPrice5: null,
                            presetPrice6: null,
                            presetPrice7: null,
                            presetPrice8: null,
                            presetPrice9: null,
                            presetPrice10: null
                        },
                        goodsCoverImage: {
                            id: null,
                            isCover: true,
                            attachmentId: null
                        },
                        goodsAttached1Image: {
                            id: null,
                            isCover: false,
                            attachmentId: null
                        },
                        goodsAttached2Image: {
                            id: null,
                            isCover: false,
                            attachmentId: null
                        },
                        goodsAttached3Image: {
                            id: null,
                            isCover: false,
                            attachmentId: null
                        },
                        goodsAttached4Image: {
                            id: null,
                            isCover: false,
                            attachmentId: null
                        },
                        goodsGoodsProperties: []
                    }
                }
            }
        },
        methods: {
            onNewModalOpen() {
                this.$refs.table.form.data.goodsCoverImage = this.form.data.goodsCoverImage;
                this.$refs.table.form.data.goodsAttached1Image = this.form.data.goodsAttached1Image;
                this.$refs.table.form.data.goodsAttached2Image = this.form.data.goodsAttached2Image;
                this.$refs.table.form.data.goodsAttached3Image = this.form.data.goodsAttached3Image;
                this.$refs.table.form.data.goodsAttached4Image = this.form.data.goodsAttached4Image;
                this.$refs.table.form.data.goodsGoodsProperties = [];
                this.$refs.propertyFormContent.defaultSelectedData = {};
            },
            onEditModalOpen() {
                this.$refs.propertyFormContent.defaultSelectedData = {};
                this.$refs.propertyFormContent.goodsPropertyGroupId = null;
            },
            onDataLoaded(data) {
                const defaultSelectedData = {};
                if(data.goodsGoodsProperties) {
                    data.goodsGoodsProperties.forEach((result)=>{
                        const goodsPropertyGroupProperties = [];
                        result.goodsGoodsPropertyValues.forEach((value)=>{
                            goodsPropertyGroupProperties.push(value.goodsPropertyValueId);
                        });
                        defaultSelectedData[result.goodsPropertyId] = goodsPropertyGroupProperties;
                    });
                }
                this.$refs.propertyFormContent.$nextTick(()=>{
                    this.$refs.propertyFormContent.defaultSelectedData = defaultSelectedData;
                })
            },
            onSelectTree(nodes) {
                if(nodes && nodes.length > 0) {
                    this.tree.selected = nodes[0];
                }
                this.loadGrid();
            },
            loadGrid() {
                this.table.queryParams.goodsCategoryId = this.tree.selected.id;
                this.$refs.table.loadGrid({
                    silent: true
                });
            },
            treeTransformResponse(response) {
                response.data.push({
                    id: 'isNull',
                    name: '未分组',
                    _addAble: false,
                    _editAble: false,
                    _deleteAble: false
                });
                return response;
            }
        },
        computed: {
            categoryData() {
                return this.$refs.goodsCategory;
            }
        },
        mounted() {
            this.$refs.goodsCategory.loadTreeData();
            this.loadGrid();
        }
    }
</script>