<style lang="less">
    @import './goods.less';
</style>

<template>
    <Card class="goods">
        <single-table ref="table"
                      :show-header="false"
                      :columns="table.columns"
                      form-title="商品维护"
                      domain-url="goods"
                      :form-rule="form.rule"
                      :form-data="form.data"
                      :modal-width="700"
                      @on-new-modal-open="onNewModalOpen"
                      @on-edit-modal-open="onEditModalOpen"
                      :form-transform-response="formTransformResponse">
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
                        <basic-form-content :form-data="props.data"></basic-form-content>
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
</template>

<script>
    import util from '@/libs/util';
    import SingleTable from '@/views/my-components/single-table/single-table.vue';
    import BasicFormContent from './basic-form-content.vue';
    import SalePriceFormContent from './sale-price-form-content.vue';
    import PropertyFormContent from './property-form-content.vue';
    import ImageFormContent from './image-form-content.vue';

    export default {
        components: {
            PropertyFormContent,
            SingleTable,
            BasicFormContent,
            SalePriceFormContent,
            ImageFormContent
        },
        data() {
            return {
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
                            return h('div', [
                                h('p', `商品名称：${row.name}`),
                                h('p', `商品编码：${row.code}`)
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
                        goodsPropertyGroupId: null,
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
            },
            formTransformResponse(response) {
                const defaultSelectedData = {};
                if(response.data.goodsGoodsProperties) {
                    response.data.goodsGoodsProperties.forEach((result)=>{
                        const goodsPropertyGroupProperties = [];
                        result.goodsGoodsPropertyValues.forEach((value)=>{
                            goodsPropertyGroupProperties.push(value.id);
                        });
                        defaultSelectedData[result.id] = goodsPropertyGroupProperties;
                    });
                    this.$refs.propertyFormContent.defaultSelectedData = defaultSelectedData;
                }
                return response;
            }
        },
        mounted() {
            this.$refs.table.loadGrid();
        }
    }
</script>